## ChangeLog

### [1.1.0] - 2017-10-02
* **Enhancements**
  * Better GStreamer error reporting. MediaPlayer used to only report   `MEDIA_ERROR_UNKNOWN`, now reports more specific errors as defined in `ErrorType.h`.
  * Codebase has been formatted for easier reading.
  * `DirectiveRouter::removeDirectiveHandler()` signature changed and now returns a bool indicating if given handler should be successfully removed or not.
  * Cleanup of raw and shared pointers in the creation of `Transport` objects.
  * `HTTP2Stream`s now have IDs assigned as they are acquired as opposed to created, making associated logs easier to interpret.
  * `AlertsCapabilityAgent` has been refactored.
      * Alert management has been factored out into an `AlertScheduler` class.
  * Creation of Reminder (implements Alert) class.
  * Added new capability agent for `PlaybackController` with unit tests.
  * Added Settings interface with unit tests.
  * Return type of `getOffsetInMilliseconds()` changed from `int64_t` to `std::chronology::milliseconds`.
  * Added `AudioPlayer` unit tests.
  * Added teardown for all Integration tests except Alerts.
  * Implemented PlaylistParser.
  
* **Bug fixes**: 

  * AIP getting stuck in `LISTENING` or `THINKING` and refusing user input on network outage.
  * SampleApp crashing if running for 5 minutes after network disconnect.
  * Issue where on repeated user barge-ins, `AudioPlayer` would not pause. Specifically, the third attempt to “Play iHeartRadio” would not result in currently-playing music pausing.
  * Utterances being ignored after particularly long TTS.
  * GStreamer errors cropping up on SampleApp exit as a result of accessing the pipeline before it’s been setup.
  * Crashing when playing one URL after another.
  * Buffer overrun in Alerts Renderer.
  * [SampleApp crashing when issuing "Alexa skip" command with iHeartRadio.](https://github.com/alexa/avs-device-sdk/issues/153)
  * [`HTTP2Transport` network thread triggering a join on itself.](https://github.com/alexa/avs-device-sdk/issues/127)
  * [`HTTP2Stream` request handling truncating exception messages.](https://github.com/alexa/avs-device-sdk/issues/67)
  * [`AudioPlayer` was attempting an incorrect state transition from `STOPPED` to `PLAYING` through a `playbackResumed`.](https://github.com/alexa/avs-device-sdk/issues/138)

* **Known Issues**

* Native components for the following capability agents are **not** included in this release: `Speaker`, `TemplateRuntime`, and `Notifications`  
* `ACL`'s asynchronous receipt of audio attachments may manage resources poorly in scenarios where attachments are received but not consumed.
* When an `AttachmentReader` does not deliver data for prolonged periods, `MediaPlayer` may not resume playing the delayed audio.
* Without the refresh token in the JSON file, the sample app crashes on start up.
* Alerts do not play after restarting the device.
* Alexa's responses are cut off by about half a second when asking "What's up" or barging into an active alarm to ask the time.
* Switching from Kindle to Amazon Music after pausing and resuming Kindle doesn't work.
* Pause/resume on Amazon Music causes entire song to start over.
* Stuck in listening state if `ExpectSpeech` comes in when the microphone has been turned off.
* Pausing and resuming Pandora causes stuttering, looped audio.
* Audible features are not fully supported.
* `Recognize` event after regaining network connection and during an alarm going off can cause client to get stuck in `Recognizing` state.
* Three Alerts integration tests fail: `handleMultipleTimersWithLocalStop`, `AlertsTest.UserLongUnrelatedBargeInOnActiveTimer`, `AlertsTest.handleOneTimerWithVocalStop`
* `MediaPlayerTest.testSetOffsetSeekableSource` unit test fails intermittently on Linux.



### [1.0.3] - 2017-09-19
* **Enhancements**
  * Implemented `setOffSet` in `MediaPlayer`.
  * [Updated `LoggerUtils.cpp`](https://github.com/alexa/avs-device-sdk/issues/77).

* **Bug Fixes**
  * [Bug fix to address incorrect stop behavior caused when Audio Focus is set to `NONE` and released](https://github.com/alexa/avs-device-sdk/issues/129).
  * Bug fix for intermittent failure in `handleMultipleConsecutiveSpeaks`.
  * Bug fix for `jsonArrayExist` incorrectly parsing JSON when trying to locate array children.
  * Bug fix for ADSL test failures with `sendDirectiveWithoutADialogRequestId`.
  * Bug fix for `SpeechSynthesizer` showing the wrong UX state when a burst of `Speak` directives are received.
  * Bug fix for recursive loop in `AudioPlayer.Stop`.

### [1.0.2] - 2017-08-23
* Removed code from AIP which propagates ExpectSpeech initiator strings to subsequent Recognize events.  This code will be re-introduced when AVS starts sending initiator strings.

### [1.0.1] - 2017-08-17

* Added a fix to the sample app so that the `StateSynchronization` event is the first that gets sent to AVS.
* Added a `POST_CONNECTED` enum to `ConnectionStatusObserver`.
* `StateSychronizer` now automatically sends a `StateSynchronization` event when it receives a notification that `ACL` is `CONNECTED`.
* Added `make install` for installing the AVS Device SDK.
* Added an optional `make networkIntegration` for integration tests for slow network (only on Linux platforms).
* Added shutdown management which fully cleans up SDK objects during teardown.
* Fixed an issue with `AudioPlayer` barge-in which was preventing subsequent audio from playing.
* Changed `Mediaplayer` buffering to reduce stuttering.
* Known Issues:
  * Connection loss during listening keeps the app in that state even after connection is regained. Pressing ‘s’ unsticks the state.
  * Play/Pause media restarts it from the beginning.
  * `SpeechSynthesizer` shows wrong UX state during a burst of Speaks.
  * Quitting the sample app while `AudioPlayer` is playing something causes a segmentation fault.
  * `AudioPlayer` sending `PlaybackPaused` during flash briefing.
  * Long Delay playing live stations on iHeartRadio.
  * Teardown warnings at the end of integration tests.
  
### [1.0.0] - 2017-08-07

* Added `AudioPlayer` capability agent.
  * Supports iHeartRadio.
* `StateSynchronizer` has been updated to better enforce that `System.SynchronizeState` is the first Event sent on a connection to AVS.
* Additional tests have been added to `ACL`.
* The `Sample App` has been updated with several small fixes and improvements.
* `ADSL` was updated such that all directives are now blocked while the handling of previous `SpeechSynthesizer.Speak` directives complete.  Because any directive may now be blocked, the `preHandleDirective() / handleDirective()` path is now used for handling all directives.
* Fixes for the following GitHub issues:
  * [EXPECTING_SPEECH + SPEAK directive simultaneously on multi-turn conversation](https://github.com/alexa/alexa-client-sdk/issues/44).
* A bug causing `ACL` to not send a ping to AVS every 5 minutes, leading to periodic server disconnects, was fixed.
* Subtle race condition issues were addressed in the `Executor` class, resolving some intermittent crashes.
* Known Issues
  * Native components for the following capability agents are **not** included in this release: `PlaybackController`, `Speaker`, `Settings`, `TemplateRuntime`, and `Notifications`.
  * `MediaPlayer`
    * Long periods of buffer underrun can cause an error related with seeking and subsequent stopped playback.
    * Long periods of buffer underrun can cause flip flopping between buffer_underrun and playing states.
    * Playlist parsing is not supported unless -DTOTEM_PLPARSER=ON is specified.
  * `AudioPlayer`
    * Amazon Music, TuneIn, and SiriusXM are not supported in this release.
    * Our parsing of urls currently depends upon GNOME/totem-pl-parser which only works on some Linux platforms.
  * `AlertsCapabilityAgent`
    * Satisfies the [AVS specification](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/timers-and-alarms-conceptual-overview) except for sending retrospective Events. For example, sending `AlertStarted` Event for an Alert which rendered when there was no internet connection.
  * `Sample App`:
      * Any connection loss during the `Listening` state keeps the app stuck in that state, unless the ongoing interaction is manually stopped by the user.
      * The user must wait several seconds after starting up the sample app before the sample app is properly usable.
  * `Tests`:
    * `SpeechSynthesizer` unit tests hang on some older versions of GCC due to a tear down issue in the test suite
    * Intermittent Alerts integration test failures caused by rigidness in expected behavior in the tests

### [0.6.0] - 2017-07-14

* Added a sample app that leverages the SDK.
* Added `Alerts` capability agent.
* Added the `DefaultClient` class.
* Added the following classes to support directives and events in the [`System` interface](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/system): `StateSynchronizer`, `EndpointHandler`, and `ExceptionEncounteredSender`.
* Added unit tests for `ACL`.
* Updated `MediaPlayer` to play local files given an `std::istream`.
* Changed build configuration from `Debug` to `Release`.
* Removed `DeprecatedLogger` class.
* Known Issues:
    * `MediaPlayer`: Our `GStreamer` based implementation of `MediaPlayer` is not fully robust, and may result in fatal runtime errors, under the following conditions:
        * Attempting to play multiple simultaneous audio streams
        * Calling `MediaPlayer::play()` and `MediaPlayer::stop()` when the MediaPlayer is already playing or stopped, respectively.
        * Other miscellaneous issues, which will be addressed in the near future
    * `AlertsCapabilityAgent`:
        * This component has been temporarily simplified to work around the known `MediaPlayer` issues mentioned above
        * Fully satisfies the AVS specification except for sending retrospective Events, for example, sending `AlertStarted` for an Alert which rendered when there was no Internet connection
        * This component is not fully thread-safe, however, this will be addressed shortly
        * Alerts currently run indefinitely until stopped manually by the user. This will be addressed shortly by having a timeout value for an alert to stop playing.
        * Alerts do not play in the background when Alexa is speaking, but will continue playing after Alexa stops speaking.
    * `Sample App`:
      * Without the refresh token being filled out in the JSON file, the sample app crashes on start up.
      * Any connection loss during the `Listening` state keeps the app stuck in that state, unless the ongoing interaction is manually stopped by the user.
      * At the end of a shopping list with more than 5 items, the interaction in which Alexa asks the user if he/she would like to hear more does not finish properly.
  * `Tests`:
    * `SpeechSynthesizer` unit tests hang on some older versions of GCC due to a tear down issue in the test suite
    * Intermittent Alerts integration test failures caused by rigidness in expected behavior in the tests

### [0.5.0] - 2017-06-23

* Updated most SDK components to use new logging abstraction.
* Added a `getConfiguration()` method to `DirectiveHandlerInterface` to register capability agents with Directive Sequencer.
* Added `ACL` stream processing with pause and redrive.
* Removed the dependency of `ACL` library on `Authdelegate`.
* Added an interface to allow `ACL` to add/remove `ConnectionStatusObserverInterface`.
* Fixed compile errors in KITT.ai, `DirectiveHandler` and compiler warnings in `AIP` tests.
* Corrected formatting of code in many files.
* Fixes for the following GitHub issues:
  * [MessageRequest callbacks never triggered if disconnected](https://github.com/alexa/alexa-client-sdk/issues/21)
  * [AttachmentReader::read() returns ReadStatus::CLOSED if an AttachmentWriter has not been created yet](https://github.com/alexa/alexa-client-sdk/issues/25)

### [0.4.1] - 2017-06-09

* Implemented Sensory wake word detector functionality.
* Removed the need for a `std::recursive_mutex` in `MessageRouter`.
* Added `AIP` unit tests.
* Added `handleDirectiveImmediately` functionality to `SpeechSynthesizer`.
* Added memory profiles for:
  * AIP
  * SpeechSynthesizer
  * ContextManager
  * AVSUtils
  * AVSCommon
* Bug fix for `MessageRouterTest` aborting intermittently.
* Bug fix for `MultipartParser.h` compiler warning.
* Suppression of sensitive log data even in debug builds. Use CMake parameter -DACSDK_EMIT_SENSITIVE_LOGS=ON to allow logging of sensitive information in DEBUG builds.
* Fixed crash in `ACL` when attempting to use more than 10 streams.
* Updated `MediaPlayer` to use `autoaudiosink` instead of requiring `pulseaudio`.
* Updated `MediaPlayer` build to suppport local builds of GStreamer.
* Fixes for the following GitHub issues:
  * [MessageRouter::send() does not take the m_connectionMutex](https://github.com/alexa/alexa-client-sdk/issues/5)
  * [MessageRouter::disconnectAllTransportsLocked flow leads to erase while iterating transports vector](https://github.com/alexa/alexa-client-sdk/issues/8)
  * [Build errors when building with KittAi enabled](https://github.com/alexa/alexa-client-sdk/issues/9)
  * [HTTP2Transport race may lead to deadlock](https://github.com/alexa/alexa-client-sdk/issues/10)
  * [Crash in HTTP2Transport::cleanupFinishedStreams()](https://github.com/alexa/alexa-client-sdk/issues/17)
  * [The attachment writer interface should take a `const void*` instead of `void*`](https://github.com/alexa/alexa-client-sdk/issues/24)

### [0.4.0] - 2017-05-31 (patch)

* Added `AuthServer`, an authorization server implementation used to retrieve refresh tokens from LWA.

### [0.4.0] - 2017-05-24

* Added `SpeechSynthesizer`, an implementation of the `SpeechRecognizer` capability agent.
* Implemented a reference `MediaPlayer` based on [GStreamer](https://gstreamer.freedesktop.org/) for audio playback.
* Added `MediaPlayerInterface` that allows you to implement your own media player.
* Updated `ACL` to support asynchronous receipt of audio attachments from AVS.
* Bug Fixes:
  * Some intermittent unit test failures were fixed.
* Known Issues:
  * `ACL`'s asynchronous receipt of audio attachments may manage resources poorly in scenarios where attachments are received but not consumed.
  * When an `AttachmentReader` does not deliver data for prolonged periods `MediaPlayer` may not resume playing the delayed audio.

### [0.3.0] - 2017-05-17

* Added the `CapabilityAgent` base class that is used to build capability agent implementations.
* Added `ContextManager`, a component that allows multiple capability agents to store and access state. These Events include `Context`, which is used to communicate the state of each capability agent to AVS in the following Events:
  * [`Recognize`](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/speechrecognizer#recognize)
  * [`PlayCommandIssued`](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/playbackcontroller#playcommandissued)
  * [`PauseCommandIssued`](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/playbackcontroller#pausecommandissued)
  * [`NextCommandIssued`](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/playbackcontroller#nextcommandissued)
  * [`PreviousCommandIssued`](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/playbackcontroller#previouscommandissued)
  * [`SynchronizeState`](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/system#synchronizestate)
  * [`ExceptionEncountered`](https://developer.amazon.com/public/solutions/alexa/alexa-voice-service/reference/system#exceptionencountered)
* Added `SharedDataStream` (SDS) to asynchronously communicate data between a local reader and writer.
* Added `AudioInputProcessor` (AIP), an implementation of a `SpeechRecognizer` capability agent.
* Added WakeWord Detector (WWD), which recognizes keywords in audio streams. [0.3.0] implements a wrapper for KITT.ai.
* Added a new implementation of `AttachmentManager` and associated classes for use with SDS.
* Updated `ACL` to support asynchronously sending audio to AVS.

### [0.2.1] - 2017-05-03

* Replaced the configuration file `AuthDelegate.config` with `AlexaClientSDKConfig.json`.
* Added the ability to specify a `CURLOPT_CAPATH` value to be used when libcurl is used by ACL and AuthDelegate.  See See Appendix C in the README for details.
* Changes to ADSL interfaces:
  * The [0.2.0] interface for registering directive handlers (`DirectiveSequencer::setDirectiveHandlers()`) was problematic because it canceled the ongoing processing of directives and dropped further directives until it completed. The revised API makes the operation immediate without canceling or dropping any handling.  However, it does create the possibility that `DirectiveHandlerInterface` methods `preHandleDirective()` and `handleDirective()` may be called on different handlers for the same directive.
  * `DirectiveSequencerInterface::setDirectiveHandlers()` was replaced by `addDirectiveHandlers()` and `removeDirectiveHandlers()`.
  * `DirectiveHandlerInterface::shutdown()` was replaced with `onDeregistered()`.
  * `DirectiveHandlerInterface::preHandleDirective()` now takes a `std::unique_ptr` instead of a `std::shared_ptr` to `DirectiveHandlerResultInterface`.
  * `DirectiveHandlerInterface::handleDirective()` now returns a bool indicating if the handler recognizes the `messageId`.
* Bug fixes:
  * ACL and AuthDelegate now require TLSv1.2.
  * `onDirective()` now sends `ExceptionEncountered` for unhandled directives.
  * `DirectiveSequencer::shutdown()` no longer sends `ExceptionEncountered()` for queued directives.

### [0.2.0] - 2017-03-27 (patch)

* Added memory profiling for ACL and ADSL.  See Appendix A in the README.
* Added a command to build the API documentation.

### [0.2.0] - 2017-03-09

* Added `Alexa Directive Sequencer Library` (ADSL) and `Alexa Focus Manager Library` (AMFL).
* CMake build types and options have been updated.
* Documentation for libcurl optimization included.

### [0.1.0] - 2017-02-10

* Initial release of the `Alexa Communications Library` (ACL), a component which manages network connectivity with AVS, and `AuthDelegate`, a component which handles user authorization with AVS.
