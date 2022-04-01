This build Qt procedure based on PhysicsX description: https://github.com/PhysicsX/QTonRaspberryPi/tree/main/QtJetsonNano5.15.0

As target OS was used Ubuntu 18.04.5 LTS (bionic)

Probably upgrade to 20.04.4 LTS will need to install less packages number.

To build was used Linaro Cross Compiler Toolchain in version 5.5.0

Instal extra packages on  target system:  ffmpeg  flex  fontconfig  libavcodec-dev  libavformat-dev  libavutil-dev libc6-dev libdbus-1-dev libexpat1 libexpat1-dev libfreetype6-dev libglib2.0-dev libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev
 libicu-dev libinput-dev libjpeg-dev libmtdev-dev libnss3-dev libopus-dev libpng-dev libpthread-workqueue-dev libpulse-dev libqt5multimedia5-plugins libre2-dev libssl-dev libssl1.0-dev libwebp-dev
 libx11-dev libx11-xcb-dev libxext-dev libxfixes-dev zlib1g-dev
 
 Instal extra packages on host system: flex ninja-build libclang-dev libnss3-dev
 
 Change two files chromium-browser sources to propertly compiling QtWebEngine:
 1.  Add two lines (marked by +) in file qt-everywhere-src-5-15-0/qtwebengine/src/3rdparty/chromium/third_party/libvpx/BUILD.gn near to line 343:
   } else if (current_cpu == "arm64") {
     if (is_chromeos || is_win ) {
         sources=libpvx_src_arm64_highbd
     } else {
+      configs -= [ "//build/config/compiler:default_symbols" ]
+      configs += [ "//build/config/compiler:no_symbols" ]
       sources = libpvx_srcs_arm64
     }
   }
   
2. Make changes in file qt-everywhere-src-5-15-0/qtwebengine/src/3rdparty/chromium/v8/src/codegen/arm64/macro-assembler-arm64.h: 
a) comment line 1973:
//    DISALOW_IMPLICIT_CONSTRUCTORS(MacroAssembler);
b) change and add (+) lines from line 162:
// using TurboAssemblerBase::TurboAssemblerBase;
+  TurboAssembler(Isolate*isolate, CodeObjectRequired create_code_object,
+                             std::unique_ptr<AssemblerBuffer> buffer = {} )
+    : TurboAssemblerBase(isolate, create_code_object, std::move(buffer)){}                            
+  TurboAssembler(Isolate*isolate, const AssemblerOptions& options,
+                             CodeObjectRequired create_code_object,
+                             std::unique_ptr<AssemblerBuffer> buffer = {} )
+    : TurboAssemblerBase(isolate, options, create_code_object, std::move(buffer)){}    

c) change and add (+) lines from line 1426:
// using TurboAssembler::TurboAssembler;
+  MacroAssembler(Isolate*isolate, CodeObjectRequired create_code_object,
+                             std::unique_ptr<AssemblerBuffer> buffer = {} )
+    : TurboAssembler(isolate, create_code_object, std::move(buffer)){}                            
+
+  MacroAssembler(Isolate*isolate, const AssemblerOptions& options,
+                             CodeObjectRequired create_code_object,
+                             std::unique_ptr<AssemblerBuffer> buffer = {} )
+    : TurboAssembler(isolate, options, create_code_object, std::move(buffer)){}    
    
Note: Machine's with many cpu cores and small RAM size can often make memory error. After that you can reinvoke compile process again. 
To prevent this limit number of cores or use computer with large RAM memory size.

Note: Read my config.summary to check witch modules and libraries should be use to compile Qt.

