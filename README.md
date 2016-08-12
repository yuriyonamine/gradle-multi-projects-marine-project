# Configurtion Concepts

There are some concepts to be put in mind that are really important to know:

Let's zoom into the configuration and execution phases of a multi-project build

For Gradle World, **configuration** means that we will configure the project using the **build.gradle** file

For that, some actions happen:

- Download all plugins declared on build.gradle

- By default, the configuration of all projects happens before any task is executed

- That means when a single task from any project is requested, all project is configured first

- In resume, **every** project is configured **before** the **execution phase**

# Configuration on demand

Configuration on demand was introduced after 1.4 version to have a new approach when a project has many subprojects, increasing in its build performance

Notice that scalability is an important requirement for Gradle

# Defining common behavior

Root project using **water** project and **bluewhale** as subproject

#### Build script of water project

```
water/
  build.gradle
  settings.gradle
  bluewhale/
```
  
But where is the **build.gradle** script fot the bluewhale project?

Notice that it is optional when we are working with subprojects. 

Of course it does not make sense when you need to buid the bluewhale project in a single way. For that you must create the build.gradle script.

Again, notice that we are indicating in water settings which project we are working on as you can see:

```groovy
// build.gradle in water project
include 'bluewhale'
```

Gradle allows you to access any project of the multi project build from any build script. This is called **cross project configuration**

Gradle implements it via **configuration injection**

To run it, you can do:

```
$ gradle -q hello
```

Obs: **-q** means quiet

#### Multi project tree

```
water/
  build.gradle
  settings.gradle
  bluewhale/
  krill/
```

Notice that we must include the project in **settings.gradle** file

```
include 'bluewhale', 'krill'
```

When you run:

```
$ gradle -q hello
```

You can get something like this:

```
I'm water
I'm bluewhale
I'm krill
```

You could like to use **allprojects.each**, since allprojects is a property that has access of all projects in the root

#### Defining common behavior

```
allprojects {
    task hello << {task -> println "I'm $task.project.name" }
}
subprojects {
    hello << {println "- I depend on water"}
}
```

Now we are using the **subprojects** property that will be called for each subproject

```
I'm water
I'm bluewhale
 - I depend on water
I'm krill
 - I depend on water
```

As you can see, the second piece doesn't use the **task** keyword, since the first one configures the base configuration on **hello** task.

#### Adding specific behavior 

Adding a specific behavion on bluewhale project

```
// in build.gradle
project(':bluewhale').hello << {
    println "- I'm the largest animal that has ever lived on this planet."
}
```

```
$ gradle -q hello
I'm water
I'm bluewhale
- I depend on water
- I'm the largest animal that has ever lived on this planet.
I'm krill
- I depend on water
```

Notice that now we have **2 sentences** on the script console. This happens because the hello task is called inside the main water project and is called in the bluewhale project

