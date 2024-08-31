# GNU Radio OOT Module Installer Demo



This repository provides very basic instructions for creating a graphical installer for your GR OOT Module via CPack for either Windows or MacOS.

Contents of this repository:

 - cmake/modules - Contains templates code to be added to your GR OOT Module in order to allow CPack to generate the correct installer
 - gr-rds - a git submodule as an example to follow along with
 - demo.patch - a patch file to apply required changes to the submodule


## Setting up your OOT Module for distribution

The first step toward making your OOT module distributable via installers is verifying the licensure of each of your dependencies that are not GR
and ensure you have met all requirements to distribute those binaries, as the installers will gather and ship all C++/Python deps you bring into your project

Once you know you can distribute your OOT module, we need to make sure that any `install` CMake commands do not use absolute paths. This makes relocation via installer
very difficult, and in general is not the best practice when writing a well formed CMake installation target.

Now once our project is ready to be relocated, we turn our attention to the `cmake/modules` directory in this repository.
There are three files there:
    * GROOTModuleCPack.cmake.in
    * OOTModuleCPackOptions.cmake.in
    * patch.wxs

The first is a CPack config file designed to do all the heavy lifting require to setup your OOT module for packaging and handle all the CPack configuration.
This file has a lot of information that is meant to be configured by a CMake `configure_file` command after the relevant variables have been defined in your project
OR alternatively, you can modify it by hand and simply add it to your project as `OOTMODULENAMECPack.cmake`. In general it is recommended to rename this file to something specific to your project.
It also should be used to establish all the packaging metadata required by OOTModuleCPackOptions.cmake.in, which is the packaing time CPack configuration file.

Once that file has been configured/added directly to the project, we want to add a few lines to our CMake:
(It is likely that you do not always want to build an installer, so we suggest scoping that behavior behind an `option(GR_BUILD_INSTALLER "Build GR OOT Module Installer" OFF)` call)

```
if(GR_BUILD_INSTALLER)
    GR_GENERATE_INSTALL()
    include(cmake/Modules/<OOTModuleName>CPack.cmake)
endif(GR_BUILD_INSTALLER)
```

The two lines within the logic are doing all the heavy lifting here. The first ensures that we're installing our binaries and bundling our dependencies, the second actually sets up CPack.
Additonally, don't forget to include any runtime dependencies in your calls to `gr_library_foo`

The final file, patch.wxs, is for Windows, and is designed to allow your installer to properly find and insert itself into a GR installation.

Thats it! You should now be able to configure and drive an OOT module installer that is capable of installing into an existing GR installation.
