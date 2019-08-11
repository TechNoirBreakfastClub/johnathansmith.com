---
title: "Laravel Commands Different Output Methods and Styles"
subtitle: What Do They Each Look Like?
date: 2019-08-10T21:06:39-04:00
draft: false
type: "post"
tags: ["laravel", "PHP", "pipeline", "commands"]
cover: https://picsum.photos/id/137/1376/800
summary: There are four command output methods. What do they look like in the CLI?
---

There are four command output methods in a  [Laravel command](https://laravel.com/docs/5.8/artisan#writing-commands):

- `line`
- `info`
- `comment`
- `question`
- `error`

Now, let's set up a test command to output this information. Start by going
to the terminal and entering `php artisan make:command OutputStyles`. 
This will create an OutputStyles class in `app/Console/Commands`. 
Now let's fill out the `handle` method. Here's what it will look like 
for this test.





