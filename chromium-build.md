```shell
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
export PATH="$PATH:/path/to/depot_tools"
mkdir ~/chromium && cd ~/chromium
fetch --nohooks chromium
cd src
git checkout -b stable_87 tags/87.0.4280.9
./build/install-build-deps.sh
gclient runhooks
gn gen out/Default

gn args out/Default

```
```
is_official_build = true
is_debug = false
symbol_level = 0
is_component_build= false
blink_symbol_level=0
disable_ftp_support=true
disable_libfuzzer=true
enable_hangout_services_extension=false
enable_nacl=false
enable_nacl_nonsfi=false
headless_use_embedded_resources = true

proprietary_codecs=true
ffmpeg_branding="Chrome"
google_api_key=""
google_default_client_id=""
google_default_client_secret=""
treat_warnings_as_errors=false
use_official_google_api_keys=false
use_unofficial_version_number=false
fieldtrial_testing_like_official_build=true
```
```shell
autoninja -C out/Default chrome
```
