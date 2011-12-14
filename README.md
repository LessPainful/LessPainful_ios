This guide explains how to setup and use LessPainful for iOS.
=============================================================

After completing this guide you will be able to run
tests locally against the iOS Simulator. You can also
interactively explore your applications using the Ruby
irb console.

Finally you will be able to build your application using special "test
target" and run that on real iOS devices via
[the LessPainful service](http://www.lesspainful.com/).

This guide takes approximately 10-15 minutes to complete.

Preparing your application.
---------------------------

To use LessPainful for iOS in your app, you must create a new target
for your app. This target links with a couple of frameworks, as
described here.

*Note*: it is important that you create a separate target as a copy the target you usually use when distributing you app to your users. Don't link with our framework in your production app - this may cause app rejection by Apple since we are using private APIs.


* Duplicate target.
 - Select your project in XCode and select your target for your app.
 - Right click (two-finger tap) your target and select "Duplicate target"
 - Select "Duplicate only" (no transition to iPad)
 - Rename your new target from ".. copy" to "..-LP"
 - From the menu select Edit Scheme and select manage schemes.
 - Rename the Scheme from ".. copy" to "..-LP"

* Target Build Settings
 - Click on your project and select your new "-LP" target.
 - Select "Build Settings".
 - Ensure that "All" and not "Basic" settings are selected in "build settings".
 - Find "Other Linker Flags" (you can type other link in the search field).
 - Ensure that "Other linker flags" contains: -all_load -lstdc++

This screenshot is a reference for you build settings.

![Build settings](https://github.com/LessPainful/LessPainful_ios/raw/master/documentation/example-2.png "Build settings")

### Linking the test target with Frameworks:

Open your application project. Expand the Frameworks folder.

You must link with the frameworks listed below. The easiest way is to simply drag the framework located in the `lib` folder of this project from finder to your Frameworks folder.

Make sure that (i) `Copy items into destination group's folder (if needed)` is _not_ checked and (ii) only your "-LP " target is checked in `Add to targets`.

Then make sure the other frameworks mentioned in the screenshot below are added (MapKit is optional).

![Build settings](https://github.com/LessPainful/LessPainful_ios/raw/master/documentation/Frameworks.png "Linking with frameworks")

![Build settings](example-4.png "Options")


### Try it out!

Make sure you select your "..-LP" scheme and then run you app on 4.x/5
iPhone simulator.

Verify that you see console output like

    2011-11-14 21:20:00.699 Example copy[12296:18203] Creating the server: <HTTPServer: 0x8995850>
    2011-11-14 21:20:00:703 Example copy[12296:18203] HTTPServer: Started HTTP server on port 37265
    2011-11-14 21:20:01:354 Example copy[12296:1a703] Bonjour Service Published: domain(local.) type(_http._tcp.) name(iLessPainful Server)


You should now be able to explore your application by installing the client as described below.

If you are having problems don't waste time! Contact `karl@lesspainful.com` ASAP :)

Installing the client.
----------------------

### Prerequisites

You need to have Ruby 1.9.2+ installed, and a recent RubyGems
installation. I use RVM to manage my Ruby installations.

For RVM, see:

 [http://beginrescueend.com/](http://beginrescueend.com/)

You also need XCode 4.x. This guide aims at XCode 4.2, but should
also work for XCode versions >= 4.0.

### Installation

*   Make sure ruby and ruby gems is on your path.

        krukow:~/examples$ ruby -v
        ruby 1.9.2p290 (2011-07-09 revision 32553) [x86_64-darwin11.1.0]
        krukow:~/examples$ gem -v
        1.8.10

*   Install `ilesspainfulclient-cucumber` gem version 0.1.3:

        krukow:~/examples$ gem install ilesspainfulclient-cucumber
        Fetching: ilesspainfulclient-cucumber-0.1.3.gem (100%)
        Successfully installed ilesspainfulclient-cucumber-0.1.3
        1 gem installed...

Exploring the sample application.
---------------------------------

Download the sample app:

(https://github.com/LessPainful/LessPainful_ios_sample_app)

You can now explore the sample application:

Let's say you've extracted the sample app file into a working
directory, `examples`.

Open the XCode project: LPSimpleExample.xcodeproj using XCode.

Select the LPSimpleExample-LP and iPhone Simulator 4.3/5.0 scheme
under schemes.

![Selecting the right scheme](https://github.com/LessPainful/LessPainful_ios/raw/master/documentation/example-1.png "Selecting Scheme")

### Run it
CMD-R to run. Look at the log output and verify that you see:

    LPSimpleExample[11298:13703] HTTPServer: Started HTTP server on port 37265

If that message is there, you're good to go.

### Play with it

The easy way is to just run one of the irb scripts: `irb_ios4.sh` or
`irb_ios5.sh`.

From this console you can explore your application interactively.

You can query, touch, scroll, etc from the irb session.
For example, notice that the sample app has a button: "Login".

### Query
If you're running the iOS5 iPhone simulator run `irb_ios5.sh` otherwise: `irb_ios4.sh`.

Now try this from the irb:

    ruby-1.9.2-p290 :003 > query("button")

You should see something like this:

    => ["<UIRoundedRectButton: 0x6567e00; frame = (109 215; 73 37); opaque = NO; autoresize = RM+BM; layer = <CALayer: 0x6567ef0>>"]

The `query` function takes a string query as an argument. They query argument is similar to a css selector, for example we can do:

    ruby-1.9.2-p290 :009 > query("button label")
     => ["<UIButtonLabel: 0x6624f40; frame = (16 9; 40 19); text = 'Login'; clipsToBounds = YES; opaque = NO; userInteractionEnabled = NO; layer = <CALayer: 0x6645ec0>>"]

It may also take parameters that are mapped to Objective-C selectors on the found object.

    ruby-1.9.2-p290 :010 > query("button label", :text)
    => ["Login"]

### Touch

Anything that can be found using query can also be touched.
Try this while you watch the iOS Simulator:

    ruby-1.9.2-p290 :011 > touch("button")

Notice that the button is touched (turns blue), although this button doesn't do anything.

You can also touch the tab bars:

    ruby-1.9.2-p290 :016 > touch("tabBarButton index:1")

The filter: `index:1` means that it is the second tab-bar button that should be touched.

### Accessibility

In general UI views are found using accesibility labels. To use those in the simulator they must be enabled.

* Press the "home-screen" button in the iOS Simulator
* Scroll left and open the Settings app insied iOS Simulator
* Select `General` > `Accessibility` > `Accessibility Inspector` : On.
* Re-run the sample app from XCode.

In your irb session try this:

    ruby-1.9.2-p290 :025 > query("view marked:'switch'")

This command finds a view with accessibility label 'switch' (not that we use single quotes to delimit the accessibility label.

In general, many views have accessibility labels that "make sense". For example the tab bar buttons have accessibility labels:

    ruby-1.9.2-p290 :029 > touch("tabBarButton marked:'second'")

To control accessibility labels on your views use:
`isAccessibilityElement = YES, and accessibilityLabel = @"somelbl";` This can be done in interface builder or programmatically:

        (void) viewDidLoad {
            [super viewDidLoad];
            self.uiswitch.isAccessibilityElement = YES;
            self.uiswitch.accessibilityLabel = @"switch";
        }

### Advanced commands

Surprisingly, these commands are enough to navigate fairly many iOS apps. However, there are many more commands available. Consult the file GherkinAPI.txt for examples of how to use these.


### Writing tests.

Test are run using Cucumber and written in a special particularly readable language: Gherkin.

In your project directory you can run the following command to setup a test directory.

    krukow:~/examples/Example/Example$ ilesspainful-skeleton
    ...

This generates a features directory containing a single test that
simply takes a screenshot. Alternatively, you can create the directory
by copying that of the LPSimpleExample.

Try running it with cucumber (`OS=ios5` if running iOS 5).

    krukow:~/examples/Example/Example$ STEP_PAUSE=2 OS=ios4 DEVICE=iphone DEVICE_ENDPOINT=http://localhost:37265 cucumber
    Feature:
        As an iOS developer
        I want to have a sample feature file
        So I can begin testing quickly

    Scenario: Example steps    # features/my_first.feature:6
        Given the app is running # ilesspainfulclient-cucumber-0.1.3/lib/ilesspainfulclient-cucumber/lesspainful_steps.rb:6
        Then take picture        # ilesspainfulclient-cucumber-0.1.3/lib/ilesspainfulclient-cucumber/lesspainful_steps.rb:155

    1 scenario (1 passed)
    2 steps (2 passed)
    0m2.366s

To see what steps are available and how they are implemented using the Ruby API see the file GherkinAPI.txt

Again, please contact `karl@lesspainful.com` with any questions or problems.
