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
