22 November, 2014

![image][1]

Back in March 2013 I wrote [A Beginner’s Guide To Grunt][2], and it’s become
the most visited article on my site. I wrote it at a time when I was just 
starting out with[Grunt][3], and it was as much a guide for myself as anyone
else. Now, 18 months later, I felt it was time to revisit how I use Grunt 
because I’ve learned a lot more in that time.

**If you’re itching to just see the code it’s all [here on Github][4].**

## Install Node and Grunt globally

First of all you will need to make sure you have [Node][5] and [Grunt CLI][6] (
command line interface) installed.

*   The Node site has various downloadable packages for different systems. 
    [Full details can be found here][5].
*   Once you’ve installed Node simply run the following command in your
    terminal of choice (I use
   [iTerm2][7]) to install **grunt-cli**:

## Install Ruby and Sass

**Update:** I’ve recently switched from using `grunt-contrib-sass` to 
`grunt-sass` which uses the faster, but experimental [libsass][8] C++ compiler
. As such, it doesn’t require Ruby or the Sass gem. However, if you experience 
issues when compiling with`grunt-sass` you should probably use 
`grunt-contrib-sass`, but in that case you will need to install Ruby and the
Sass gem.

I use Sass as my CSS preprocessor. In order to use the Sass Grunt task you will
need to install Ruby
([full installation instructions can be found here][9]) and, once you’ve done
that, the[Sass][10] gem:

## Create the project directories

Our project requires a couple of directories to be set up. Mirror the structure
below:

    grunt/
    src/
    src/images/
    src/scripts/
    src/styles/
    

## Create a Gruntfile

First of all, I don’t use ‘scaffolding’ tools any more (such as `grunt init` or
[Yeoman][11]). I set up everything from scratch, which means I have a much
greater understanding of what’s going on now. It’s really not that hard once you
’ve done it a few times.

In the root of your project create a file called `Gruntfile.js`.

In that file add the following code:

    <span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="kd">function</span><span class="p">(</span><span class="nx">grunt</span><span class="p">)</span> <span class="p">{</span>
    
        <span class="nx">require</span><span class="p">(</span><span class="s1">'time-grunt'</span><span class="p">)(</span><span class="nx">grunt</span><span class="p">);</span>
    
        <span class="nx">require</span><span class="p">(</span><span class="s1">'load-grunt-config'</span><span class="p">)(</span><span class="nx">grunt</span><span class="p">,</span> <span class="p">{</span>
            <span class="nx">jitGrunt</span><span class="o">:</span> <span class="kc">true</span>
        <span class="p">});</span>
    <span class="p">};</span>
    

Believe it or not, that’s it as far as our Gruntfile is concerned!

`time-grunt` tells you how much time each task and the total build has taken,
and`jitGrunt: true` tells `load-grunt-config` to use the faster [jit-grunt][12]

## Create a package file

Let’s move on and also create our basic `package.json` file. This file will
shortly contain our project’s dependencies. Add the following (obviously change 
references to ‘my project’ to the actual name of your project
):

    <span class="p">{</span>
      <span class="s2">"name"</span><span class="o">:</span> <span class="s2">"my-project"</span><span class="p">,</span>
      <span class="s2">"version"</span><span class="o">:</span> <span class="s2">"0.0.1"</span><span class="p">,</span>
      <span class="s2">"description"</span><span class="o">:</span> <span class="s2">"My project"</span>
    <span class="p">}</span>
    

## Add some dependencies

Now we have all we need to be able to start adding some modules. Run each of
the lines of code below, one after the other:

    npm install grunt --save-dev
    npm install <span class="nb">time</span>-grunt --save
    npm install load-grunt-config --save-dev
    npm install grunt-concurrent --save-dev
    npm install grunt-contrib-clean --save-dev
    npm install grunt-contrib-imagemin --save-dev
    npm install grunt-sass --save-dev
    npm install grunt-contrib-uglify --save-dev
    

If you look in `package.json` you should now see something like this:

    <span class="p">{</span>
      <span class="s2">"name"</span><span class="o">:</span> <span class="s2">"my-project"</span><span class="p">,</span>
      <span class="s2">"version"</span><span class="o">:</span> <span class="s2">"0.0.1"</span><span class="p">,</span>
      <span class="s2">"description"</span><span class="o">:</span> <span class="s2">"My project"</span><span class="p">,</span>
      <span class="s2">"devDependencies"</span><span class="o">:</span> <span class="p">{</span>
        <span class="s2">"grunt"</span><span class="o">:</span> <span class="s2">"^0.4.5"</span><span class="p">,</span>
        <span class="s2">"grunt-concurrent"</span><span class="o">:</span> <span class="s2">"^1.0.0"</span><span class="p">,</span>
        <span class="s2">"grunt-contrib-clean"</span><span class="o">:</span> <span class="s2">"^0.6.0"</span><span class="p">,</span>
        <span class="s2">"grunt-contrib-imagemin"</span><span class="o">:</span> <span class="s2">"^0.8.1"</span><span class="p">,</span>
        <span class="s2">"grunt-contrib-uglify"</span><span class="o">:</span> <span class="s2">"^0.6.0"</span><span class="p">,</span>
        <span class="s2">"grunt-sass"</span><span class="o">:</span> <span class="s2">"^0.16.1"</span><span class="p">,</span>
        <span class="s2">"load-grunt-config"</span><span class="o">:</span> <span class="s2">"^0.13.1"</span>
      <span class="p">},</span>
      <span class="s2">"dependencies"</span><span class="o">:</span> <span class="p">{</span>
        <span class="s2">"time-grunt"</span><span class="o">:</span> <span class="s2">"^1.0.0"</span>
      <span class="p">}</span>
    <span class="p">}</span>
    

Here is a summary of what we’ve just installed:

*   `grunt`: The task runner itself.
*   `time-grunt`: This isn’t required, but it’s a neat addition - it tells
    you how much time each task and the total build has taken.
   
*   `load-grunt-config`: Allows you to keep our main Gruntfile short and
    succinct. More on that in a bit.
   
*   `grunt-concurrent`: Run tasks concurrently - Out-of-the-box Grunt will run
    each task one after the other, which can take a while depending on the amount 
    and type of tasks you need to run. However, there are often tasks that are not 
    dependent on other tasks which can be run at the same time.
   
*   `grunt-contrib-clean`: Quite simply, this task deletes ‘stuff’ - use
    with caution!
   
*   `grunt-contrib-imagemin`: Indispensible for all your image optimisation
    needs.
   
*   `grunt-sass`: Compiles your SASS/SCSS files into CSS. **Please note:** This
    Sass task uses the faster, but more experimental
   [libsass][8] compiler. If you experience problems you should probably use
    the more stable, but slower
   [grunt-contrib-sass][13] task instead.
*   `grunt-contrib-uglify`: Makes your Javascript nice and ugly.

## Configure the tasks

One of the best modules I’ve discovered is `load-grunt-config`. It allows us to
put the config for each of our tasks in separate files, which is far more 
manageable than having everything in one long Gruntfile.

In the `grunt` directory create the following files:

    grunt/aliases.yaml
    grunt/concurrent.js
    grunt/clean.js
    grunt/imagemin.js
    grunt/sass.js
    grunt/uglify.js
    

**Note: The names of these files must match the names of the tasks.**

Copy and paste the config for each task below into the relelvant file.

### aliases.yaml

    <span class="l-Scalar-Plain">default</span><span class="p-Indicator">:</span>
      <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">prod</span>
    <span class="l-Scalar-Plain">dev</span><span class="p-Indicator">:</span>
      <span class="p-Indicator">-</span> <span class="s">'concurrent:devFirst'</span>
      <span class="p-Indicator">-</span> <span class="s">'concurrent:devSecond'</span>
    <span class="l-Scalar-Plain">img</span><span class="p-Indicator">:</span>
      <span class="p-Indicator">-</span> <span class="s">'concurrent:imgFirst'</span>
    <span class="l-Scalar-Plain">devimg</span><span class="p-Indicator">:</span>
      <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">dev</span>
      <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">img</span>
    <span class="l-Scalar-Plain">prod</span><span class="p-Indicator">:</span>
      <span class="p-Indicator">-</span> <span class="s">'concurrent:prodFirst'</span>
      <span class="p-Indicator">-</span> <span class="s">'concurrent:prodSecond'</span>
      <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">img</span>
    

What we’re doing here is defining various aliases for our tasks:

*   `default` - Runs the `prod` tasks when you run `grunt` on the command line
    .
*   `dev` - Runs the development tasks (but not image tasks)
*   `img` - Runs the image tasks
*   `devimg` - Runs the development and image tasks
*   `prod` - Runs the production and image tasks

**[Click here][14] for more information on configuring task aliases for 
`load-grunt-config`.**

### concurrent.js

    <span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="p">{</span>
    
        <span class="c1">// Task options</span>
        <span class="nx">options</span><span class="o">:</span> <span class="p">{</span>
            <span class="nx">limit</span><span class="o">:</span> <span class="mi">4</span>
        <span class="p">},</span>
    
        <span class="c1">// Dev tasks</span>
        <span class="nx">devFirst</span><span class="o">:</span> <span class="p">[</span>
            <span class="s1">'clean'</span>
        <span class="p">],</span>
        <span class="nx">devSecond</span><span class="o">:</span> <span class="p">[</span>
            <span class="s1">'sass:dev'</span><span class="p">,</span>
            <span class="s1">'uglify'</span>
        <span class="p">],</span>
    
        <span class="c1">// Production tasks</span>
        <span class="nx">prodFirst</span><span class="o">:</span> <span class="p">[</span>
            <span class="s1">'clean'</span>
        <span class="p">],</span>
        <span class="nx">prodSecond</span><span class="o">:</span> <span class="p">[</span>
            <span class="s1">'sass:prod'</span><span class="p">,</span>
            <span class="s1">'uglify'</span>
        <span class="p">],</span>
    
        <span class="c1">// Image tasks</span>
        <span class="nx">imgFirst</span><span class="o">:</span> <span class="p">[</span>
            <span class="s1">'imagemin'</span>
        <span class="p">]</span>
    <span class="p">};</span>
    

Taking the dev tasks as an example, you can see that they are set up to run 
`clean` first, and then `sass:dev` and `uglify` concurrently to regenerate the
css and javascript.

**[Click here][15] for more information on configuring `grunt-concurrent`.**

### clean.js

    <span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="p">{</span>
        <span class="nx">all</span><span class="o">:</span> <span class="p">[</span>
            <span class="s2">"dist/"</span>
        <span class="p">]</span>
    <span class="p">};</span>
    

Configuring `grunt-contrib-clean` is quite simple. Here I’m just removing all
the contents of the`dist/` directory. Use this task with caution - it will
indiscriminately delete whatever you tell it to without any warnings, so make 
sure you configure it correctly.

**[Click here][16] for more information on configuring `grunt-contrib-clean`.**

### imagemin.js

    <span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="p">{</span>
        <span class="nx">all</span><span class="o">:</span> <span class="p">{</span>
            <span class="nx">files</span><span class="o">:</span> <span class="p">[{</span>
                <span class="nx">expand</span><span class="o">:</span> <span class="kc">true</span><span class="p">,</span>
                <span class="nx">cwd</span><span class="o">:</span> <span class="s1">'src/'</span><span class="p">,</span>
                <span class="nx">src</span><span class="o">:</span> <span class="p">[</span><span class="s1">'images/*.{png,jpg,gif}'</span><span class="p">],</span>
                <span class="nx">dest</span><span class="o">:</span> <span class="s1">'dist/images/'</span>
            <span class="p">}]</span>
        <span class="p">}</span>
    <span class="p">};</span>
    

The config above simply optimises all images in `src/images/` and saves them to
`dist/images/`.

**[Click here][17] for more information on configuring `grunt-contrib-imagemin`
**

### sass.js

    <span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="p">{</span>
        <span class="c1">// Development settings</span>
        <span class="nx">dev</span><span class="o">:</span> <span class="p">{</span>
            <span class="nx">options</span><span class="o">:</span> <span class="p">{</span>
                <span class="nx">outputStyle</span><span class="o">:</span> <span class="s1">'nested'</span><span class="p">,</span>
                <span class="nx">sourceMap</span><span class="o">:</span> <span class="kc">true</span>
            <span class="p">},</span>
            <span class="nx">files</span><span class="o">:</span> <span class="p">[{</span>
                <span class="nx">expand</span><span class="o">:</span> <span class="kc">true</span><span class="p">,</span>
                <span class="nx">cwd</span><span class="o">:</span> <span class="s1">'src/styles'</span><span class="p">,</span>
                <span class="nx">src</span><span class="o">:</span> <span class="p">[</span><span class="s1">'*.scss'</span><span class="p">],</span>
                <span class="nx">dest</span><span class="o">:</span> <span class="s1">'dist/styles'</span><span class="p">,</span>
                <span class="nx">ext</span><span class="o">:</span> <span class="s1">'.css'</span>
            <span class="p">}]</span>
        <span class="p">},</span>
        <span class="c1">// Production settings</span>
        <span class="nx">prod</span><span class="o">:</span> <span class="p">{</span>
            <span class="nx">options</span><span class="o">:</span> <span class="p">{</span>
                <span class="nx">outputStyle</span><span class="o">:</span> <span class="s1">'compressed'</span><span class="p">,</span>
                <span class="nx">sourceMap</span><span class="o">:</span> <span class="kc">false</span>
            <span class="p">},</span>
            <span class="nx">files</span><span class="o">:</span> <span class="p">[{</span>
                <span class="nx">expand</span><span class="o">:</span> <span class="kc">true</span><span class="p">,</span>
                <span class="nx">cwd</span><span class="o">:</span> <span class="s1">'src/styles'</span><span class="p">,</span>
                <span class="nx">src</span><span class="o">:</span> <span class="p">[</span><span class="s1">'*.scss'</span><span class="p">],</span>
                <span class="nx">dest</span><span class="o">:</span> <span class="s1">'dist/styles'</span><span class="p">,</span>
                <span class="nx">ext</span><span class="o">:</span> <span class="s1">'.css'</span>
            <span class="p">}]</span>
        <span class="p">}</span>
    <span class="p">};</span>
    

I’ve split the Sass task into development and production workflows. The
config is very similar, but for development purposes I’ve set the output style 
to`nested` and activated source maps.

**[Click here][18] for more information on configuring `grunt-sass`.**

### uglify.js

    <span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="p">{</span>
        <span class="nx">all</span><span class="o">:</span> <span class="p">{</span>
            <span class="nx">files</span><span class="o">:</span> <span class="p">{</span>
                <span class="s1">'dist/scripts/main.min.js'</span><span class="o">:</span> <span class="p">[</span><span class="s1">'src/scripts/main.js'</span><span class="p">]</span>
            <span class="p">}</span>
        <span class="p">}</span>
    <span class="p">};</span>
    

The uglify task simply takes Javascript files and minifies them - simples!

**[Click here][19] for more information on configuring `grunt-contrib-uglify`**

## Run the tasks

If you’ve finished setting up your project as outlined above then you can now
run the tasks. As discussed earlier there are various task aliases you can run. 
For now just run`grunt` on the command line from the root of your project.

All being well you should see a load of text scroll up the screen and then
finish with a message that looks something like this.

![Grunt Production Build Output][20]

I love the little summary that `time-grunt` provides. I can see how long each
concurrent task-set took to run, plus how long the whole build process took - 
neat!

Depending on your requirements you could also choose to run `grunt dev`, 
`grunt devimg`, or `grunt img`.

## Summary

And that’s about all there is to it really. If you experiment with the above
you’ll soon gain confidence, start adding new tasks and modifying the workflow 
to better suit your own requirements.

**Once again, the code accompanying this article can also be found 
[on Github][4].**

Leave any questions you have in the comments below, or file issues on 
[github][21].

 [1]: img/tumblr_inline_mjrobcZQpo1qz4rgp.png

 [2]: http://mattbailey.io/a-beginners-guide-to-grunt/ "Matt Bailey: A Beginner's Guide To Grunt"
 [3]: http://gruntjs.com/ "Grunt"
 [4]: https://github.com/matt-bailey/grunt-frontend-boilerplate
 [5]: http://nodejs.org/download/
 [6]: http://gruntjs.com/getting-started
 [7]: http://iterm2.com/
 [8]: http://libsass.org/
 [9]: https://www.ruby-lang.org/en/installation/
 [10]: http://sass-lang.com/download.html
 [11]: http://yeoman.io/
 [12]: https://github.com/shootaroo/jit-grunt
 [13]: https://github.com/gruntjs/grunt-contrib-sass
 [14]: https://github.com/firstandthird/load-grunt-config#aliases
 [15]: https://github.com/sindresorhus/grunt-concurrent
 [16]: https://github.com/gruntjs/grunt-contrib-clean
 [17]: https://github.com/gruntjs/grunt-contrib-imagemin
 [18]: https://github.com/sindresorhus/grunt-sass
 [19]: https://github.com/gruntjs/grunt-contrib-uglify
 [20]: img/grunt-frontend-boilerplate-1.png
 [21]: https://github.com/matt-bailey/grunt-frontend-boilerplate/issues