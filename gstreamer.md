# fetch

### git
> tested with b3389ed
1. https://gitlab.freedesktop.org/gstreamer/gstreamer
2. https://github.com/GStreamer/gstreamer

# build

### meson
```sh
# comment
gpl=enabled ugly=disabled tools=enabled
gst-plugins-base:gl_{platform=egl,api=gles2,winsys="egl,wayland"}
gst-plugins-{base:gl=enabled,good:autodetect}=enabled

gst-plugins-bad:{debugutils,subenc,videoparsers,faad,openh264}=enabled
gst-plugins-base:{app,audioconvert,audioresample,playback,typefind,videoconvertscale,volume}=enabled
gst-plugins-good:{audiofx,audioparsers,isomp4}=enabled

gst-plugins-bad:{aom,fdkaac}=enabled
gst-plugins-base:{ogg,opus,vorbis}=enabled
gst-plugins-good:{avi,matroska,amrnb,amrwbdec,lame,flac,wavparse,mpg123}=enabled
```

# patches

### muon support
```diff
diff --git a/scripts/meson.build b/scripts/meson.build
index c6a875b..fed6f63 100644
--- a/scripts/meson.build
+++ b/scripts/meson.build
@@ -18,14 +18,8 @@ release_modules = [
 
 # Make sure the files are all identical to avoid divergence
-gen_cl_hash = fs.hash(files('gen-changelog.py'), 'md5')
 
 out_of_sync_list = []
 
 foreach m : release_modules
-  module_gen_cl_hash = fs.hash(f'../subprojects/@m@/scripts/gen-changelog.py', 'md5')
-
-  if module_gen_cl_hash != gen_cl_hash
-    out_of_sync_list += [f'subprojects/@m@/scripts/gen-changelog.py']
-  endif
 endforeach
 
diff --git a/subprojects/gst-plugins-base/scripts/meson-pkg-config-file-fixup.py b/subprojects/gst-plugins-base/scripts/meson-pkg-config-file-fixup.py
index ef92d89..f66741e 100755
--- a/subprojects/gst-plugins-base/scripts/meson-pkg-config-file-fixup.py
+++ b/subprojects/gst-plugins-base/scripts/meson-pkg-config-file-fixup.py
@@ -35,6 +35,4 @@ build_root = os.environ['MESON_BUILD_ROOT']
 # Poking into the private dir is not entirely kosher of course..
 pc_files = [
-  os.path.join(build_root, 'meson-private', pc_name + '.pc'),
-  os.path.join(build_root, 'meson-uninstalled', pc_name + '-uninstalled.pc')
 ]
```

### debloat
```diff
diff --git a/subprojects/gst-plugins-bad/ext/meson.build b/subprojects/gst-plugins-bad/ext/meson.build
index 6df06c2..918b31a 100644
--- a/subprojects/gst-plugins-bad/ext/meson.build
+++ b/subprojects/gst-plugins-bad/ext/meson.build
@@ -79,2 +78,0 @@ subdir('wayland')
-subdir('webrtc')
-subdir('webrtcdsp')
diff --git a/subprojects/gst-plugins-bad/gst-libs/gst/meson.build b/subprojects/gst-plugins-bad/gst-libs/gst/meson.build
index f320252..3d2fdd0 100644
--- a/subprojects/gst-plugins-bad/gst-libs/gst/meson.build
+++ b/subprojects/gst-plugins-bad/gst-libs/gst/meson.build
@@ -8,7 +7,0 @@ subdir('codecs')
-subdir('d3dshader')
-subdir('d3d11')
-subdir('d3d12')
-# cuda can depend on d3d11
-subdir('cuda')
-subdir('dxva')
-subdir('hip')
@@ -28,3 +20,0 @@ subdir('wayland')
-subdir('webrtc')
-subdir('winrt')
-subdir('analytics')
diff --git a/subprojects/gst-plugins-bad/sys/meson.build b/subprojects/gst-plugins-bad/sys/meson.build
index 57370ac..cd10abc 100644
--- a/subprojects/gst-plugins-bad/sys/meson.build
+++ b/subprojects/gst-plugins-bad/sys/meson.build
@@ -7,3 +6,0 @@ subdir('bluez')
-subdir('d3d11')
-subdir('d3d12')
-subdir('d3dvideosink')
@@ -16 +12,0 @@ subdir('fbdev')
-subdir('hip')
diff --git a/subprojects/gstreamer/libs/gst/helpers/meson.build b/subprojects/gstreamer/libs/gst/helpers/meson.build
index 7d46b81..90cb95c 100644
--- a/subprojects/gstreamer/libs/gst/helpers/meson.build
+++ b/subprojects/gstreamer/libs/gst/helpers/meson.build
@@ -48,3 +47,0 @@ gst_scanner_dir = meson.current_build_dir()
-install_data(['gst_gdb.py', 'glib_gobject_helper.py'],
-  install_dir : join_paths(get_option('datadir'), 'gstreamer-@0@'.format(api_version), 'gdb'),
-  install_tag : 'devel')
@@ -70,5 +66,0 @@ endif
-configure_file(input : 'libgstreamer-gdb.py.in',
-  output : 'libgstreamer-@0@.so.@1@-gdb.py'.format(api_version, libversion),
-  install_dir : gdb_install_dir,
-  install_tag : 'devel',
-  configuration : gdbconf)
```
