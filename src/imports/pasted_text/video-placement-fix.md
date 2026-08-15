Please use the existing Figma Make chat history and the current app version as the source of truth. This app has gone through many iterations already, so do not rebuild or redesign the existing experience from scratch. Fix the current implementation while preserving the existing visual design, navigation, interactions, typography, colors, and overall look and feel unless specifically instructed below.

1. Fix the video dimensions and placement

The videos are currently bugged/mis-sized. The video area needs to behave like it did in the original Lovable build.

* Create a dedicated video/media section wherever the app is supposed to display instructional/content videos.
* The video should NOT be placed inside the same area/card that contains the questions.
* The video should display as an actual portrait-format video, using the video’s natural aspect ratio rather than forcing it into the dimensions of a question/card.
* Use a standard vertical/portrait video proportion, approximately 9:16, unless the existing video asset’s native dimensions indicate otherwise.
* The entire video frame must be visible. Do not crop the top, bottom, left, or right of the video.
* Do not stretch or distort the video.
* Do not put an iPhone/device frame, mockup, or phone overlay around the video.
* The video itself should be the visual element displayed on screen.
* Preserve the full video playback, including audio.
* The video should have appropriate play/pause and playback behavior based on how it worked in the original Lovable build.
* Make sure the video container is sized around the video rather than forcing the video into the existing question dimensions.
* If the current implementation is using a fixed image/question container that is causing the video to be cropped, replace that behavior with a responsive video container.

2. Separate videos from questions

There needs to be a clear distinction between media/video content and question content.

The current bug appears to be treating the video/image area like it is another question/card component.

Change the structure so that:

Video section
→ Displays the full portrait video with audio.

Question section
→ Displays the question using the existing Figma question design.

Do NOT combine these into one component or force the video into the question’s image placeholder.

3. Keep the existing Figma question design

For actual quiz questions:

* Keep the current Figma Make question layout/design.
* Keep the multiple-choice answers.
* Keep the existing answer-selection interactions.
* Keep the existing styling and hierarchy.
* Do not replace the quiz UI with the old Lovable question UI.

The goal is to preserve the current Figma question experience while fixing how the media/video content is displayed.

4. Remove multiple-choice questions from Flip Cards

The Flip Cards section should NOT contain multiple-choice questions.

Flip Cards should function as cards that can be flipped to reveal their intended content, following the existing Flip Cards design.

Remove/hide any:

* Multiple-choice answer buttons
* Quiz-style answer selections
* Question/answer options that were accidentally carried over from the Quiz component

Do not redesign the Flip Cards unnecessarily. Just make sure the quiz functionality is not being injected into this section.

5. Fix the “Look at Cards” section

The Look at Cards section should also NOT contain multiple-choice quiz functionality.

Remove any multiple-choice questions/options from this section while preserving the existing card layout and interactions.

The multiple-choice functionality should exist ONLY in the dedicated Quiz section.

6. Component/content separation

Please make sure the app is logically separated into these content types:

VIDEOS

* Dedicated video/media area
* Full portrait video
* Full video visible
* Audio works
* No question UI inside the video area

QUIZ

* Questions
* Multiple-choice answers
* Existing Figma Make quiz design

FLIP CARDS

* Flip-card interaction
* No multiple-choice answers

LOOK AT CARDS

* Card viewing experience
* No multiple-choice answers

Do not allow the components/content from one section to accidentally appear inside another section.

7. Most important visual requirement

The biggest issue to fix is the video sizing.

Think of the desired result as:

[FULL PORTRAIT VIDEO]

not:

[QUESTION CARD]
[small/cropped video inside question image area]
[multiple-choice answers]

The video should look like the original Lovable implementation where the actual full vertical video is displayed prominently on screen with its audio, rather than being treated like a question image.

8. Do not break the existing app

Because this is already a later version of the app with extensive existing Figma Make chat history:

* Inspect the existing implementation before making changes.
* Reuse existing components and assets where possible.
* Do not reset/rebuild the application.
* Do not remove existing functionality that is unrelated to these fixes.
* Do not change the overall design system.
* Do not create duplicate pages or duplicate components.
* Do not replace working functionality just to implement these fixes.
* Preserve everything that is already working.

Final goal: Keep the current Figma Make design for the quiz/questions, but restore the video experience to the way it worked in the original Lovable build: a dedicated, properly sized portrait video area showing the complete video with audio, completely separate from the questions. Multiple-choice functionality should exist only in the Quiz section and nowhere in Flip Cards or Look at Cards.