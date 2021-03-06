#### AppBundler


AppBundler is an Ant task for packaging up desktop Java apps as native
executables.  It can package Mac OS X .app bundles, Windows EXEs, JNLPs,
and double clickable jars that work anywhere.

The Basics
----------

To use AppBundler, create a bundle.xml in your project directory like this:

```xml
<app name="SimpleTest">
    <jar name="simpletest.jar" 
        main-class="com.joshondesign.appbundler.simpletest.Main"/>
    <jar name="XMLLib.jar"/>
</app>
```

The bundle.xml lists the jars which make up your app. The jar with your main
class must have a 'main-class' attribute.

Now create an ant target in your build.xml that looks like this:

```xml
<target name='package-app' depends='build'>
    <taskdef name='appbundler' 
        classname='com.joshondesign.appbundler.BundlerTask'
        classpath='build/classes;lib/XMLLib.jar'/>
        
    <appbundler
        bundle="bundle.xml"
        target="mac"
        destdir="dist"
        libdir="build/jars;lib"
    />
</target>
```

That's it. Just run `ant package-app` and a Mac bundle will appear in dist/mac.

Valid targets are `mac`, `win`, `jnlp`, `onejar`, and `all`.

The attributes 'bundle', 'target', 'destdir', and 'libdir' must be set. 
'libdir' is a semicolon separated list of directories which contain your jars.


Native Libraries
----------------

To use native libraries like JavaFX or Jogl you must put the jars and native 
libs in a specific structure. Create a directory in your lib directory with
the name of your lib. Under that put a directory called 'native' and under that
put a dir each for mac, win, and linux.  Put the shared jars in the named lib dir.
Put the native libraries and platform specific jars in the os specific dir. 

For example, for JavaFX support in LeoSketch I created this directory structure:
```
lib
    javafx
        native
            linux
                jfxrt.jar
                fxplugins.so
                libprism-es2.so
                ...
            mac
                jfxrt.jar
                fxplugins.dylib
                libprism-es2.dylib
                ...
            win
                jfxrt.jar
                fxplugins.dll
                prism-es2.dll
                ...
```

Now you can refer to this native lib from your bundle.xml by adding a single line

```xml
<app name="SimpleTest">
    <jar name="simpletest.jar" 
        main-class="com.joshondesign.appbundler.simpletest.Main"/>
    <jar name="XMLLib.jar"/>
    <native name="javafx"/>
</app>

```

AppBundler will then copy over the correct jars and native libs for the 
particular target platform.
                

