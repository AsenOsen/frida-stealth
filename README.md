# Stealth patch for Frida!

The patch set was inspired by this repo - https://github.com/JsHookApp/Frida-Patchs. This patch hides most of frida traces in system:

- default port number changed
- unix socket names (named pipes) does not contain "frida" no more
- all thread names does not contain "frida" no more
- frida-gum main loop renamed
- GLib main loop renamed
- SELinux context names

# How to apply patch?

Get latest Frida version with all submodules:

```
git clone --recurse-submodules https://github.com/frida/frida.git
```

Apply patches:

```
cd frida/frida-core && git apply --reject --whitespace=fix ../../frida-core/patch.patch
cd frida/frida-gum && git apply --reject --whitespace=fix ../../frida-gum/patch.patch
```

# How to build Frida with patch? (Android Arm64 example)

1. Download Android NDK. Easiest way to do it - via Android Studio SDK Manager: pick the NDK version 25.2.9519653.
2. In root frida folder: 
	- Build: `ANDROID_NDK_ROOT="$ANDROID_HOME/ndk/25.2.9519653" make core-android-arm64` 
	- Run the script to hide frida symbols in compiled binaries: 
		- Install requirements: `pip install lief` 
		- Run `python src/anti-anti-frida.py PATH` for following `PATH` list (you may pass unneeded components):
			- Agent library: `build/tmp-android-arm64/frida-core/lib/agent/frida-agent.so`
			- Gadget library: `build/tmp-android-arm64/frida-core/lib/gadget/frida-gadget.so`
			- Frida Inject: `build/frida-android-arm64/bin/frida-inject`
			- Frida Portal: `build/frida-android-arm64/bin/frida-portal`
			- Frida Server: `build/frida-android-arm64/bin/frida-server`
	- Copy needed binaries to Android Device and enjoy the patch!

# Advices how to achive even more stealthy mode

1. Use ZygiskFrida(https://github.com/lico-n/ZygiskFrida) to avoid process being ptraced. Important notice: use this module for Kitsune Magisk (https://github.com/HuskyDG/magisk-files). Do not use ZygiskFrida with usual Magisk - module will not work.
2. When using ZygiskFrida, use patched `frida-gadget.so`!
3. Use frida scripts from this repo - https://github.com/apkunpacker/AntiFrida_Bypass - to hide any other possible traces of frida/magisk in memory.
4. Consider some uncommon techniques like [patching system libraries](https://github.com/AsenOsen/android-framework-jar-patching).
