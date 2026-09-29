===============================================================================
 Trip Planner (Flutter package: fyp2_flutter)
 README — Installation and Execution Guide
===============================================================================

Trip Planner is an Android smart travel-planning app. Users create a trip, add
destinations (search, Google Maps link, map tap, or AI text extraction), assign
them to days, and let the app compute an efficient visiting order with walking,
driving, cycling, or public-bus routing. Trips are saved to the cloud and can be
resumed later.

Platform: Android only. An internet connection is required at run time (the app
relies on cloud and public web services — see "External Services").

This README explains how to install the tools, configure the services, and run
the project from source. It is an execution guide, not a project report.

-------------------------------------------------------------------------------
 1. REQUIRED TOOLS (with versions and official download links)
-------------------------------------------------------------------------------

The versions below match those used to build and verify the project.

  Flutter SDK          3.38.5 (stable channel)
                       https://docs.flutter.dev/get-started/install
                       (Flutter bundles the matching Dart SDK — see below.)

  Dart SDK             >= 3.11.4  (bundled with Flutter; no separate install)
                       https://dart.dev/get-dart
                       The pubspec requires Dart SDK ^3.11.4.

  Android Studio       2025.2.2 (Patch 1)
                       https://developer.android.com/studio
                       Used to install the Android SDK, manage an emulator, and
                       run/deploy the app.

  Android SDK          Platform + build-tools (installed via Android Studio's
                       SDK Manager). Flutter selects the compile/target SDK
                       automatically; install Android SDK Platform 34 or newer.
                       https://developer.android.com/studio

  Java Development Kit  JDK 17  (the Android build targets Java 17)
                       https://adoptium.net/temurin/releases/?version=17
                       Android Studio also ships a compatible JDK; Temurin 17 is
                       a standalone alternative.

  Visual Studio Code   1.108.0 (optional — primary code editor)
                       https://code.visualstudio.com/
                       Install the "Flutter" and "Dart" extensions.

  Git                  latest
                       https://git-scm.com/downloads

  Firebase CLI         latest (optional — only if you re-generate Firebase config)
                       https://firebase.google.com/docs/cli

  Gradle               8.14 — NO manual install required. The Android Gradle
                       wrapper downloads it automatically on the first build.

After installing Flutter and Android Studio, run "flutter doctor" and resolve
any reported issues before continuing.

-------------------------------------------------------------------------------
 2. PROJECT DEPENDENCIES
-------------------------------------------------------------------------------

All Flutter/Dart packages are declared in pubspec.yaml and installed
automatically. From the project root, run:

    flutter pub get

The dependencies fall into a few groups:
  - Firebase        - authentication and cloud storage
  - Google Maps     - map display and Google Maps link parsing
  - Networking      - REST calls to Gemini, OSRM and Google APIs
  - Public transit  - GTFS static and realtime parsing
  - Utilities       - local storage, environment loading, and UI helpers

Notes:
  - The Gemini API is called directly over REST; there is no separate Gemini SDK
    package to install.
  - No package needs manual configuration beyond "flutter pub get".
  - After running "flutter pub get", all required Flutter packages are installed automatically.

-------------------------------------------------------------------------------
 3. ENVIRONMENT CONFIGURATION (do this before running)
-------------------------------------------------------------------------------

3.1 Firebase project
    - Create a Firebase project in the Firebase Console
      (https://console.firebase.google.com).
    - In Authentication, enable the Email/Password sign-in method.
    - Create a Cloud Firestore database.
    - Register an Android app with the package name:  com.example.fyp2_flutter
    - Download the generated google-services.json (see 3.2).

3.2 google-services.json  (Firebase Android config)
    Place the file at:
        android/app/google-services.json
    (A file is already present; replace it with your own project's file if you
    use a different Firebase project.)

3.3 Google Maps / Google Cloud API keys  (Google Cloud Console)
    - In Google Cloud Console (https://console.cloud.google.com), enable:
        Maps SDK for Android, Places API, Geocoding API.
    - Create an API key.
    - The interactive map (Maps SDK for Android) uses the key configured in the
      Android manifest at:
        android/app/src/main/AndroidManifest.xml
      in the meta-data entry:
        android:name="com.google.android.geo.API_KEY"
    - In addition to the map, the application uses the Google Places API (to
      search for destinations) and the Google Geocoding API (to convert between
      addresses and coordinates). Configuring the manifest key alone enables the
      map but not these two services.
    - If you build against your own Google Cloud project, make sure the
      corresponding API key(s) for Places and Geocoding are updated accordingly,
      so that all three Google services work.

3.4 .env  (Gemini API key)
    - Get a Gemini API key from Google AI Studio
      (https://aistudio.google.com/app/apikey).
    - Create a file named  .env  in the project root (fyp2_flutter/.env) with:
        GEMINI_API_KEY=YOUR_GEMINI_API_KEY
    The .env file is registered as a Flutter asset and is loaded at start-up.

    IMPORTANT: Do not commit real API keys to source control.

-------------------------------------------------------------------------------
 4. EXTERNAL SERVICES
-------------------------------------------------------------------------------

  Service                  Why it is needed                 Internet  Local setup
  -----------------------  -------------------------------  --------  -----------
  Firebase Authentication   Email/password sign-in           Yes       No (cloud)
  Cloud Firestore           Saving / loading trips           Yes       No (cloud)
  Google Maps SDK (Android) Interactive map display          Yes       No (API key)
  Google Places API         Search destinations by name      Yes       No (API key)
  Google Geocoding API      Address <-> coordinate lookup    Yes       No (API key)
  Gemini API                AI destination extraction        Yes       No (API key)
  OSRM                      Walking/driving/cycling routing  Yes       No — uses
                                                                       PUBLIC OSRM
                                                                       servers
  GTFS (Malaysia Open       Public-bus schedules + realtime   Yes      No — feeds
  Data Platform,                                                       are DOWNLOADED
  api.data.gov.my)                                                     at run time

  Important: OSRM and GTFS require NO local server or bundled data. The app calls
  public OSRM endpoints and downloads the Malaysia GTFS feeds over the internet
  automatically. You do not need to host OSRM or place any GTFS files locally.

-------------------------------------------------------------------------------
 5. SETUP AND RUN
-------------------------------------------------------------------------------

  1. Clone or extract the project, then open a terminal in the project root:
         git clone <repository-url>
         cd fyp2_flutter

  2. Open the project in VS Code or Android Studio (optional but recommended).

  3. Install the dependencies:
         flutter pub get

  4. Set up Firebase: place your google-services.json in android/app/
     and make sure Email/Password auth and Cloud Firestore are enabled
     (Section 3.1 - 3.2).

  5. Configure the API keys:
       - add your Google Cloud key(s) for the Maps SDK, Places API and
         Geocoding API                                    (Section 3.3);
       - create the .env file with GEMINI_API_KEY          (Section 3.4).

  6. Connect an Android device (USB debugging enabled) or start an Android
     emulator from Android Studio. Confirm it is detected:
         flutter devices

  7. Run the application:
         flutter run

     To build a release APK instead:
         flutter build apk

  No additional OSRM or GTFS setup is required (see Section 4).

-------------------------------------------------------------------------------
 6. REQUIRED FILES AND ASSETS
-------------------------------------------------------------------------------

File / asset                              Location                    	Provided?
----------------------------------------  --------------------------  	---------
google-services.json                      android/app/                	Yes*
.env (GEMINI_API_KEY)                     project root (fyp2_flutter/) 	You create
Google Cloud API configuration            AndroidManifest.xml and     	You configure
                                          Google Cloud project
assets/data/malaysia_cities.json          bundled with the project     	Yes

  * Replace with your own Firebase project's file if you do not use the bundled
    configuration.

-------------------------------------------------------------------------------
 7. TROUBLESHOOTING (common issues)
-------------------------------------------------------------------------------

  - "flutter pub get" fails:
        Run "flutter doctor" and ensure the Flutter/Dart SDK is on the stable
        channel and provides Dart >= 3.11.4. Check your internet connection.

  - Map does not load / grey map:
        The Google Maps API key is missing or invalid in AndroidManifest.xml,
        or the Maps SDK for Android is not enabled in Google Cloud Console.

  - Place search or address lookup fails:
        The Places API and/or Geocoding API is not enabled, or the corresponding
        Google Cloud key has not been updated for your own project (Section 3.3).

  - Firebase / sign-in errors:
        google-services.json is missing from android/app/, or Email/Password
        auth or Cloud Firestore is not enabled in the Firebase Console.

  - AI destination extraction does nothing:
        The .env file is missing or GEMINI_API_KEY is not set. Confirm .env is
        in the project root and re-run.

  - No bus routes appear:
        Public-transport data is downloaded over the internet; ensure the
        device/emulator is online. Buses are only available for supported
        Malaysian regions.

  - Emulator has no internet:
        Cold-boot the emulator or check the host machine's network; the app
        needs internet for maps, routing, GTFS and cloud services.

-------------------------------------------------------------------------------
 8. PLATFORM REQUIREMENTS AND LIMITATIONS
-------------------------------------------------------------------------------

  - Android only.
  - An internet connection is required at run time.
  - Trip planning currently supports Malaysia only (country/state selection is
    limited to a predefined set; public transport covers supported Malaysian
    regions).
  - Authentication is email/password only.


 End of README

