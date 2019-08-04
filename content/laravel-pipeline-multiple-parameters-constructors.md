---
title: Laravel Pipeline With Multiple Parameters and Constructors
subtitle: Pass Data with Multiple Params and Differing Constructors
date: 2019-07-23T03:03:18.687Z
draft: true
type: post
tags: ["laravel", "PHP", "pipeline", "design pattern"]
description:
  A Laravel specific trick to tame erratic functionality. Allows for testable
  pipelines that utilize the service container and multiple params.
image: 
---

This is a cool Laravel specific trick I have created which may come in handy for a lot of use cases. It is a cross between the [Laravel pipeline](https://jeffochoa.me/understanding-laravel-pipelines) and the [decorator pattern](https://laracasts.com/series/design-patterns-in-php/episodes/1). For me, I had to continually edit data as if it were going through pipes or middleware, however there were some gotchas. I needed to:
- Pass multiple parameters
- Initialize certain objects
- Potentially pass by reference
- Have it be testable (always)

Now, with Laravel pipelines, we can pass data amongst several objects and return it. However, we can only pass [one piece of data or object](https://github.com/illuminate/pipeline/blob/master/Pipeline.php#L59).

I want to pass multiple items, one specifically would be a `Request` object. Cool, we can technically get this through a constructor with dependency injection, so maybe we want to pass another item which has already been modified, some sort of custom object.

The first thing we will want to do is create an interface that has a shared method amongst all these object. Just like the standard Laravel pipeline, but this time we will pass multiple parameters.

```php
interface EditsPipeline {
    public function edit(Request $request, array $data): array;
}
```

Now let's create an object that does stuff to our data.

```php
class DataEditor implements EditsPipeline {
    public function edit(Request $request, array $data): array {
        $data['stuff'] = $request->get('param1');
        return $data;
    }
}
```

Awesome! Now we can edit the array and pass it back! But say we have another class that requires constructor params. Well, let's add that.

```php
class DataManipulator implements EditsPipeline {

    private $object;
    
    public function __construct(NecessaryObject $object)
    {
        $this->object = $object;
    }
    
    public function edit(Request $request, array $data): array {
        $data['more_stuff'] = $request->get('param2');
        $data['other_stuff'] = $this->getFunky();
        return $data;
    }
    
    private function getFunky(): ?array
    {
        return $this->object->funky() ?? null;
    }
    
}
```

Woohoo! Now we can get it from the injected item via the service container. So how would something like this look like in a project? Here ya go...

```php
class Items {
    private $request;
    public function __construct(Request $request)
    {
        $this->request = $request;
    }
    
    public function run(): array
    {
        $data = [];
        foreach($this->getPipes() as $pipe) {
            $data = app($pipe)->edit($data);
        }
        return $data;
    }
    
    private function getPipes(): array
    {
        /** put in as many classes as you need **/
        return [
            DataEditor::class,
            DataManipulator::class,
        ];
    }
}
```

Please note in the run function, we are initializing the array outside the loop. We are then using `app()` to get the class from the service container. And since we are using the same interface, we just call the method on the initialized object. Anything inside the constructor of these objects will automatically be initialized too. If you need super custom controller then this technique may not work for you.

A quick optimization: If you have a BUNCH of long arrays that may be taking up a lot of memory, it may be best to pass by reference.

```php
interface EditsPipeline {
    public function edit(Request $request, array &$data): void;
}
```

So, pretty cool, right? This method also makes classes infinitely testable. We'll use unit tests.

```php
class DataManipulatorTest extends TestCase
{
    public function testEdit()
    {
        $dataManipulator = app(DataManipulator::class);
        $request = request();
        $data = [];
        $data = $dataManipulator->edit($request, $data);
        $this->assertIsArray($data);
        $this->assertArrayHasKey('more_stuff', $data);
        $this->assertArrayHasKey('other_stuff', $data);
        $this->assertNotEmpty($data['more_stuff']);
    }
}
```

So you may not need this. It may be a bit overkill. When possible, it's best to create objects and do things via object oriented programming methodology. However, if there are a series of steps, where things may be erratic, this technique may be helpful.

\- Johnathan
