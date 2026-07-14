---
title: "Three Days with NixOS: Why It Feels Different"
date: 2026-07-13T18:00:00+02:00
draft: false
description: "After three days of daily development on NixOS, a backend engineer shares first impressions, what worked, and why the declarative approach feels fundamentally different."
tags:

- nixos
- linux
- kde
- kotlin
- nextjs
- ocaml
- gleam
- development
- devops
- infrastructure-as-code
- reproducibility

categories:
- Linux
- Development

author: Johan

---

For the past few years I've primarily used Ubuntu and Kubuntu for my development machines. Like many backend engineers, I don't really care which Linux distribution I'm using—as long as it stays out of my way.

A few weeks ago I started looking at alternatives. Fedora was on my shortlist, but I kept coming across developers talking about NixOS. I'd heard about its declarative configuration and reproducible systems before, but I had always assumed it was something aimed at Linux enthusiasts rather than people who just wanted to get work done.

Three days later, I'm beginning to understand why people are so passionate about it.

I'm not ready to recommend NixOS yet. Three days isn't enough time to judge the long-term stability of any operating system. But it is enough time to recognize that NixOS approaches the problem of managing a computer in a fundamentally different way.

## A Real Development Machine

I didn't install NixOS to browse the web.

I installed it to replace my daily development environment.

Over the last three days I've been working on several different projects:

* Kotlin backend development using IntelliJ IDEA
* Next.js development
* OCaml development
* Gleam development
* Docker-based services
* Git, Gradle and the usual developer tooling

This wasn't a toy installation inside a virtual machine. It was intended to answer one question:

> Can I actually do my job on NixOS?

So far, the answer has been yes.

Once I had the machine configured, I simply started developing. There haven't been any unexpected issues with my toolchains, desktop environment or IDE.

That alone impressed me.

## The Learning Curve Is Real

I won't pretend the first day was easy.

NixOS forces you to think differently.

Instead of installing packages manually, you describe the system you want.

Instead of editing configuration files across the filesystem, you define them in configuration modules.

Instead of wondering what changed six months ago, the configuration itself becomes the source of truth.

It took some time before that mental model clicked.

Once it did, the operating system started making much more sense.

## My Operating System Became Source Code

This is the part that genuinely excites me.

Most operating systems accumulate state over time.

You install packages.

You edit configuration files.

You tweak settings.

You forget why something was installed.

Eventually you have a machine that works, but nobody—including yourself six months later—can explain exactly how it got there.

NixOS approaches the problem differently.

My desktop is now described by source code.

My configuration is split into modules:

* applications
* development tools
* Docker
* KDE Plasma
* fonts
* Kubernetes
* NVIDIA configuration
* host-specific settings

The operating system has started to look like one of my software projects.

That's a surprisingly satisfying feeling.

## A Familiar Way of Thinking

As a backend engineer, this resonates with me.

Over the last few years I've become increasingly interested in ideas like:

* functional programming
* immutable data
* infrastructure as code
* reproducible builds
* declarative configuration

Those ideas have influenced how I write Kotlin applications.

What surprised me is that NixOS applies many of the same principles to the operating system itself.

Instead of telling the computer *how* to become a certain state, I describe *what* the desired state should be.

That shift feels very natural once you've spent time working with modern software engineering practices.

## Reproducibility Changes Everything

Perhaps the biggest advantage isn't today's machine.

It's tomorrow's.

I have two laptops:

* my personal ASUS laptop
* my Dell work laptop

Once I'm satisfied that my configuration is stable, reproducing my environment on the Dell becomes dramatically simpler.

Both machines can share the same configuration while keeping only the hardware-specific pieces separate.

If I decide to install a new development tool, I don't need to remember to repeat the process on multiple machines.

I update the configuration.

Rebuild.

Done.

That is a very different experience from maintaining multiple Linux installations manually.

## It's Still Early

I'm intentionally keeping my enthusiasm in check.

Three days is enough to form a first impression.

It is not enough to evaluate:

* long-term system updates
* kernel upgrades
* desktop upgrades
* driver updates
* months of daily development

Those are the things that determine whether an operating system becomes a long-term home.

I want to spend several more weeks using NixOS before making that judgement.

## Final Thoughts

After three days, what stands out isn't a particular feature.

It's the philosophy.

For years I've been trying to make my applications more reproducible, more declarative and easier to reason about.

NixOS applies those same ideas to the operating system.

For the first time, my desktop feels less like a collection of manually maintained state and more like a software project under version control.

Whether NixOS ultimately becomes my permanent operating system remains to be seen.

But after only a few days, I already understand why so many developers find it compelling.

It doesn't just change how you install software.

It changes how you think about your computer.
