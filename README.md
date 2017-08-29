## mruby for iOS

This is a project that will build the current mruby source into a XCode
framework. That framework can then be used to embed Ruby into an iOS
application.

For a complete example of using the product of the build check out
[MRubyiOSExample](https://github.com/carsonmcdonald/MRubyiOSExample)

This is not an attempt to make a bridge between Objective-C and Ruby, if you
want that then check out the [mobiruby](http://mobiruby.org/) project.


## Install with Cocoapods
`pod 'mruby', '~> 1.3'`

## Custom Build

* git clone
* rake

After the above steps you should have a complete MRuby.framework framework
structure that is ready to use.

## Example Use

Assume you have the following simple Ruby script you want to execute:

```
puts "Hello world"
```

First you need to compile the script into byte code using the mrbc command
that can be found in the bin directory after you have compiled the project:

```
mrbc helloworld.rb
```

The output of that command is a bytecode file that can be run using the mruby
command found in the same bin directory. NB you currently need to cat all your
script files together before compiling the, using require doesn't work.

Once you have compiled your Ruby code and added it to your application bundle
you can embed it in your app using something like the following code:

```
#include "mruby/mruby.h"
#include "mruby/mruby/proc.h"
#include "mruby/mruby/dump.h"

// ...

    NSString *bcfile = [[[NSBundle mainBundle] resourcePath] stringByAppendingPathComponent:@"helloworld.mrb"];
    
    mrb_state *mrb = mrb_open();    
    FILE *fp = fopen([bcfile UTF8String], "rb");
    if (fp == NULL) {
        NSLog(@"Error loading file...");
    } else {
        int n = mrb_read_irep_file(mrb, fp);
        fclose(fp);
        
        mrb_run(mrb, mrb_proc_new(mrb, mrb->irep[n]), mrb_top_self(mrb));
    }
```

## Notes

* mruby is new and changing constantly, don't be surprised if this project
  doesn't build.
* Currently you can't use the mruby compiler while embedded in an arm7 device.
  That is why you need to pre-compile the code using mrbc. It will work on the 
  simulator but don't let that fool you into thinking it will work on a device. 
  An app created to compile code would almost certainly have other issues.

## License

MIT to match the mruby license. See the LICENSE file for full license.
