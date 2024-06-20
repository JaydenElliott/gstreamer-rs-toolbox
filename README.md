# 🧰 Gstreamer Toolbox (Rust and C) 🧰

**Note: The list below is currently in progress**

A collection of useful documentation, tools, code snippets and articles for writing Gstreamer applications. Anyone deep enough in the gstreamer ecosystem knows how difficult it can be to find up to date documentation or examples on specific plugins/tools. Hopefully this guide can give you a hand navigating this space.

⚙️ Feel free to update the list with any resources you might have with a PR ⚙️

## Table of contents

1. [Start Here](#start-here)
2. [Primary Sources](#primary-sources)
   - [Core Gstreamer Docs](#core-gstreamer-docs)
   - [Gst-inspect-1.0](#gst-inspect-10-cli)
   - [Rust Bindings](#rust-bindings)
   - [Rust Plugins](#rust-plugins)
3. [Common Questions](#common-questions)
4. [Observability](#observability)
   - [Tracers](#tracers)
   - [Dot Graphs](#dot-graphs)
5. [Debugging Pipelines](#debugging-pipelines)
   - [GST_DEBUG](#gst_debug)
   - [Debug Probe](#debugprobe-plugin)
   - [Gst Devtools](#debugprobe-plugin)
   - [GstShark](#gstshark)
6. [Profiling and Optimisation](#primary-sources)
7. [Testing](#testing)
   - [GstHarness](#gstharness)
   - [GstValidate](#gstvalidate)
8. [Nvidia Deepstream](#nvidia-deepstream)
9. [IDE / Environment](#environment-setup)

<br>

## Start Here

Todo

[Rust Tutorial](https://gitlab.freedesktop.org/gstreamer/gst-plugins-rs/-/tree/main/tutorial)

Prepare to read **a lot** of plugin implementations and examples. Although there is a fair bit of documentation, it won't answer a lot of questions you will have about specifics. My usual workflow for findings answers is:

<br>

# Primary Sources

> 95% of your questions can be answered by these

## Core Gstreamer Docs

[Core Gstreamer Documentation](https://gstreamer.freedesktop.org/documentation/)

For high level design overviews and core/custom plugin documentation. This should usually be your first point of call. Useful docs:

- [Basic concepts (pads, elements, pipelines etc...)](https://gstreamer.freedesktop.org/documentation/application-development/introduction/basics.html?gi-language=c)
- [Architecture and design overview](https://gstreamer.freedesktop.org/documentation/additional/design/overview.html?gi-language=c)
- [Tutorial (in c)](https://gstreamer.freedesktop.org/documentation/tutorials/index.html?gi-language=c) or [rust port](https://gitlab.freedesktop.org/gstreamer/gstreamer-rs/-/tree/main/tutorials/src/bin)
- [API reference](https://gstreamer.freedesktop.org/documentation/libs.html?gi-language=c)
- [Plugin reference](https://gstreamer.freedesktop.org/documentation/plugins_doc.html?gi-language=c)

<br>

## Gst-inspect-1.0 CLI

[Gst-inspect-1.0 Documentation](https://gstreamer.freedesktop.org/documentation/tools/gst-inspect.html?gi-language=c)

[Example Pipelines](https://gist.github.com/hum4n0id/2760d987a5a4b68c24256edd9db6b42b)

Command line access to plugin documentation and allows you to query your own custom plugins to check:

- Description
- Pad capabilities
- Properties
- Signals

This will only work for plugins you have installed (see: [installing a custom plugin in rust](https://gitlab.freedesktop.org/gstreamer/gst-plugins-rs#building) or [in c](https://gstreamer.freedesktop.org/documentation/plugin-development/basics/boiler.html?gi-language=c#the-plugin_init-function)).

<br>

## Rust Bindings

[Gstreamer Rust Bindings](https://gitlab.freedesktop.org/gstreamer/gstreamer-rs)

This repository, as well as [gst-plugins-rs](https://gitlab.freedesktop.org/gstreamer/gst-plugins-rs) are, imo the most useful references for writing your own plugins. @sdroege has done a momentous job building these bindings and writing excellent tutorials/documentation. I frequently visit the plugin examples and find the following resources extremely useful for newcomers and experienced gst devs alike:

- [Examples of gst api usage from rust](https://gitlab.freedesktop.org/gstreamer/gstreamer-rs/-/tree/main/examples/src/bin)
- [Installing the bindings](https://gitlab.freedesktop.org/gstreamer/gstreamer-rs#installation)
- [Gstreamer tutorial code rewritten in rust](https://gitlab.freedesktop.org/gstreamer/gstreamer-rs/-/tree/main/tutorials/src/bin)
- [Rust docs](https://gstreamer.pages.freedesktop.org/gstreamer-rs/stable/latest/docs/gstreamer/)
  <br>

<br>

## Rust Plugins

[Gstreamer Rust Plugins](https://gitlab.freedesktop.org/gstreamer/gst-plugins-rs)

Contains endless plugin examples, a great tutorial and is constantly active. Core maintainers are very reponsive if you have any questions not covered in their examples/docs.

- [Plugin examples](https://gitlab.freedesktop.org/gstreamer/gst-plugins-rs)
- [Fantastic tutorial on writing gst plugins in rust](https://gitlab.freedesktop.org/gstreamer/gst-plugins-rs/-/tree/main/tutorial)
- [Tracer examples](https://gitlab.freedesktop.org/gstreamer/gst-plugins-rs/-/tree/main/utils)

<br>

# Messages and Events

Messages are passed directly to the applications message bus. Events are passed up/down the pipeline and can trigger events.

For example, a QOS event send out by a sink at the end of your pipeline, could be received by a decoder who would send a `Message` to the message bus with stats on how many frames it has processed and dropped.
 
# Observability

## Tracers

Tracers are extremely useful for monitoring pipeline throughput, element latency, cpu usage and a variety of other metrics.

- [Gstreamer tracing docs](https://gstreamer.freedesktop.org/documentation/additional/design/tracing.html?gi-language=c)
  - Not great but has some good [examples](https://gstreamer.freedesktop.org/documentation/additional/design/tracing.html?gi-language=c#try-it) of usage. These will work directly with a rust application.

- [Rust tracing integration](https://github.com/standard-ai/tracing-gstreamer)
   - Better formatted logs using `fmttracing`
   - `chrometracer` for pipeline profiling. Use a tool like [perfetto](https://ui.perfetto.dev/) to visualise the results
   
- [GstShark Tracer Plugins](https://developer.ridgerun.com/wiki/index.php/GstShark_-_Tracers)
  - Set of plugins that extend/replace some of the core gstreamer ones. Very well documented.

- [todo - tracer graphs (not pipeline ones)](https://gstreamer.freedesktop.org/documentation/check/gstharness.html?gi-language=c)

- [todo - devtool tracer parser](https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/main/subprojects/gst-devtools/tracer)
  - have been unable to get this working

 ## Measuring Video and Performance

 - [todo - write up on other tools]
 - [Measure framerate, bitrate and CPU usage](https://github.com/RidgeRun/gst-perf)


<br>

## Dot Graphs

[Core Dot Graph Documentation](https://gstreamer.freedesktop.org/documentation/gstreamer/debugutils.html?gi-language=c)

### From a rust application

1. Define `GST_DEBUG_DUMP_DOT_DIR` environment variable

2. Upcast pipeline to `gst::Element` then call `element.debug_to_dot_file()`. Example usage from [gstreamer-rs playbin example](https://gitlab.freedesktop.org/gstreamer/gstreamer-rs/-/blob/main/examples/src/bin/playbin.rs#L129):

```
let bin_ref = playbin.downcast_ref::<gst::Bin>().unwrap();
bin_ref.debug_to_dot_file(gst::DebugGraphDetails::all(), "PLAYING");

```

### Using GstShark

[Easy pipeline image generation](https://developer.ridgerun.com/wiki/index.php/GstShark_-_Tracers)

- todo: check works in non-gst-launch environment

<br>

<br>

# Debugging Pipelines

## GST_DEBUG

[Debug Logs Documentation](https://gstreamer.freedesktop.org/documentation/tutorials/basic/debugging-tools.html?gi-language=c#the-debug-log)

Used to set the gstreamer specific log levels. Can log everything from basic errors/info logs to every event and message on the event bus.

These can be easily viewed using the [debug-viewer](https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/main/subprojects/gst-devtools/debug-viewer). A small app for filtering and sorting through GST logs.

<br>

## DebugProbe plugin

TODO

## gst-devtools

[gst-devtools](https://gitlab.freedesktop.org/gstreamer/gstreamer/-/tree/main/subprojects/gst-devtools)

## GstShark

[todo - debugging](https://developer.ridgerun.com/wiki/index.php/GStreamer_Debugging#GStreamer_Element_Debugging)
[todo - gstd](https://github.com/RidgeRun/gstd-1.x)
[todo - gstd2](https://developer.ridgerun.com/wiki/index.php/GStreamer_Daemon)

> "GStreamer Daemon is gst-launch on steroids"

<br>

# Profiling and Optimisation

[GstShark](https://developer.ridgerun.com/wiki/index.php/Embedded_GStreamer_Performance_Tuning)

<br>

# Testing

## GstHarness

[GstHarness](https://gstreamer.freedesktop.org/documentation/check/gstharness.html?gi-language=c)

## GstValidate

[GstValidate Tutorial](https://blogs.igalia.com/aboya/2019/05/14/validateflow-a-new-tool-to-test-gstreamer-pipelines/)

<br>

## Nvidia Deepstream

[Deepstream SDK](https://developer.nvidia.com/deepstream-sdk)

todo

# Environment Setup

A lot of your time will be spent reading, compiling and testing `c/c++`. I would suggest setting up your IDE to enable quick definition/ reference jumping (the promise of rust bindings doesn't let you escape cpp completely unfortunately).

todo: vscode devcontainer setup for gstreamer/nvidia
todo: nvim container setup

<br>

# TODO 
- split up into files
- timestamps
    - https://stackoverflow.com/questions/76997707/why-doesnt-the-pts-retrieved-from-gstreamer-doesnt-match-saved-ts
    - https://stackoverflow.com/questions/74367999/how-to-correlate-a-gstreamer-buffer-timestamp-with-the-linux-system-clock
