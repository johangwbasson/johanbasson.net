---
title: "SDKMan"
date: 2022-08-21T18:46:50+02:00
draft: false
hero: sdk-man.svg
description: "SDKMan"
---

I came across a tool named [SDKMAN](https://sdkman.io/) which makes my life as a Java developer much much easier.

I use Linux as my primary operating system and always install the Java JDK from the repositories. The problem that was faced with, was having multiple Java JDK kits available on my machine as the projects that I work with requires different Java Development Kit versions.

SDKMAN includes various Java Development Kits from different vendors each with multiple versions.

SDKMan gives you more than just JDK installations, it comes with the ability to install build tools, java development kits, scala development kits and various other tools.

To get started you run this command:

```bash
curl -s "https://get.sdkman.io" | bash
```

This will install the application to your home directory. To access it you run:

```bash
sdk
```

To list all available JDK's run this command:

```bash
sdk list java
```

You install a Jdk using this command: 

```bash
sdk install java 17.0.4.fx-zulu  
```

This will install the Azul Java JDK

