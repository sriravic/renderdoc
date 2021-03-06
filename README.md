RenderDoc
==============

Welcome to RenderDoc - a graphics debugger, currently available for D3D11 development on windows. 

Quick Links:

* **Builds & Downloads**: https://renderdoc.org/builds
* **Documentation**: renderdoc.chm in the build, or http://docs.renderdoc.org/
* **Tutorials**: There are some video tutorials on Youtube: http://www.youtube.com/user/baldurkarlsson/
* **Email contact**: baldurk@baldurk.org
* **IRC channel**: #renderdoc on freenode
* **Roadmap/future development**: [Roadmap](https://github.com/baldurk/renderdoc/wiki/Roadmap)

API Support
--------------

|                  | Status                            | Windows                  | Linux                           |
| ---------------- | --------------------------------- | ------------------------ | ------------------------------- |
| D3D11            | Well supported, all features.     | :heavy_check_mark:       | :heavy_multiplication_x:        |
| OpenGL 3.2 core+ | *Work in progress, not complete.* | :heavy_check_mark:       | :heavy_check_mark: No native UI |
| OpenGL Pre-3.2   | No immediate plans                | :heavy_multiplication_x: | :heavy_multiplication_x:        |
| D3D10            | No immediate plans                | :heavy_multiplication_x: | :heavy_multiplication_x:        |
| D3D9             | No immediate plans                | :heavy_multiplication_x: | :heavy_multiplication_x:        |

* D3D11 has full feature support and is stable & tested. Feature Level 11 hardware is assumed - Radeon 4000/5000+, GeForce 400+, Intel Ivy Bridge.
* OpenGL currently is only aiming for support of the 3.2 Core profile and above (up to 4.5) - this means no compatibility features will be deliberately supported.
* Currently only some entry points and functionality is supported on OpenGL, improving this to match D3D11 and support the full OpenGL API is on-going. Only Core and ARB extensions will be definitely supported, other extensions like EXT and per-vendor will be supported case-by-case as desired.
* At the moment we assume that a 4.3 or higher core profile is supported by the machine, even if the captured program only uses 3.2, and we use extensions for this assumption as relevant.
* Linux and Windows are both supported for OpenGL, although the UI only runs on Windows currently.

Downloads
--------------

There are [binary releases](https://renderdoc.org/builds) available, built from the release targets. If you just want to use the program and you ended up here, this is what you want :).

It's recommended that if you're new you start with the stable builds. Beta builds are available for those who want more regular updates with the latest features and fixes, but might run into some bugs as well. Nightly builds are available every day from master branch here if you need it.

License
--------------

RenderDoc is released under the MIT license, see LICENSE.md for full text as well as 3rd party library acknowledgements.

Building
--------------

Building RenderDoc is fairly straight forward.

### Windows ###

The main [renderdoc.sln](renderdoc.sln) is a VS2010 solution. To build on later VS versions, simply open & upgrade, I've tested building on VS2012 and VS2013 without issues.

The only external dependency should be the Windows 8.1 SDK. The 8.0 SDK should also work fine, but [the vcxproj](renderdoc/renderdoc.vcxproj) is set up to look in `$(ProgramFiles)\Windows Kits\8.1\` for the necessary paths. If your SDK is installed elsewhere you'll also need to change these locally. You can also compile only against the June 2010 DirectX SDK if you undefine `INCLUDE_D3D_11_1` in `d3d11_common.h`.

Profile is recommended for day-to-day dev. It's debuggable but not too slow. Release is obviously what you should build for any builds you'll send out to people or if you want to evaluate performance.

### Linux ###

Just 'make' in the root should do the trick. This build system is work in progress as the linux port is very early, so it may change!

Contributing & Development
--------------

There are always plenty of things to do, if you'd like to chip in! Check out the [Roadmap](https://github.com/baldurk/renderdoc/wiki/Roadmap) page in the wiki for future tasks to tackle, or have a look at the [issues](https://github.com/baldurk/renderdoc/issues) for outstanding bugs. I'll try and tag things that seem like small changes that would be a good way for someone to get started with.

If you have a change you'd like to see make it into mainline, just open a pull request on github. We can discuss changes if there need to be any, then merge it in. Please make sure your changes are fully rebased against master when you create the pull request.

If you're tackling anything large then please contact me and post an issue so that everyone knows you're working on it and there's not duplicated effort. *Specifically* if you want to extend RenderDoc to a platform or API that it doesn't already support please get in touch, as there might already be code that isn't committed yet. Particularly if this is not a public API that anyone can write against.

Official releases will get a github release made for them, nightly builds are just marked with the hash of the commit they were built from.

Please don't distribute releases marked with a version number as it will confuse me with auto-submitted crashes since I won't have the symbols for them. If you distribute releases leave the version information as is (master is always marked as an unofficial non-versioned build).

Code Explanation
--------------

There are [several pages](https://github.com/baldurk/renderdoc/wiki/Code-Dives) on the wiki explaining different aspects of how the code fits together - like how the capture-side works vs replay-side, how shader debugging works, etc.

    renderdoc/ 
        dist.sh                         ; a little script that will build into dist/ with everything necessary
                                        ; to distribute a build - assumes that exes etc are already built
        renderdoc/
            3rdparty/                   ; third party utilities & libraries included
            ...                         ; everything else in here consists of the core renderdoc runtime
        renderdoccmd/                   ; A small C++ utility program that runs to do various little tasks
        renderdocui/                    ; The .NET UI layer built on top of renderdoc/
        pdblocate/                      ; a simple stub program to invoke DIA to look up symbols/pdbs
                                        ; for callstack resolution on windows
        docs/                           ; source documentation for the .chm file or http://docs.renderdoc.org/
                                        ; in the Sandcastle help file builder
        installer/                      ; installer scripts for WiX Toolset
        ScintillaNET/                   ; .NET component for using Scintilla in the shader viewers
        WinFormsUI/                     ; dockpanelsuite for docking UI
        breakpad/                       ; parts of google breakpad necessary for crash reporting system

Tips
--------------

### Visual Studio Visualisers ###

You might find these visualisers useful, going under your [Visualizer] section in autoexp.dat:

    rdctype::wstr { preview([$e.elems,su]) stringview([$e.elems,su]) }
    rdctype::str { preview([$e.elems,s]) stringview([$e.elems,s]) }

    rdctype::array<*> {
        preview ( #( "[",$e.count,"] {", #array(expr: $e.elems[$i], size: $e.count), "}") )
        children ( #( #([size] : $e.count), #array(expr: $e.elems[$i], size: $e.count) ) )
    }

