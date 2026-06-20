name: Build Real Android APK
on:
  repository_dispatch:
    types: [compile_apk]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Repository
      uses: actions/checkout@v3

    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'

    - name: Install Cordova (Asli App Builder Engine)
      run: |
        npm install -g cordova
        cordova create myApp com.kk.engine AI_App_KK
        cd myApp
        cordova platform add android
        
    - name: Inject Gemini Generated Code
      run: |
        echo "${{ github.event.client_payload.prompt }}" > myApp/www/index.html

    - name: Build Real Android APK
      run: |
        cd myApp
        cordova build android --release

    - name: Upload Finished APK
      uses: actions/upload-artifact@v3
      with:
        name: KK-User-App
        path: myApp/platforms/android/app/build/outputs/apk/release/

