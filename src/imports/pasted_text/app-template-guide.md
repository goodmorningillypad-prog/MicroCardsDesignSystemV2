IMPORTANT: FIX THE APP PREVIEW, PRESERVE THE ORIGINAL APP, AND PREPARE THIS AS A STYLISTIC TEMPLATE

The current build is being used as a visual and stylistic template for an existing app that will ultimately be merged back into our Lovable/GitHub codebase. Do NOT treat this build as a replacement for the existing app’s functionality.

1. REMOVE THE IPHONE MOCKUP/OVERLAY ENTIRELY

The biggest issue is the current iPhone frame/overlay.

Do NOT render the app inside a decorative iPhone mockup, phone frame, device bezel, or simulated phone shell.

The preview should show only the actual app interface/screen itself.

The app should:

* Look like the actual application, not a screenshot of an application inside an iPhone.
* Be fully interactive directly from the screen.
* Have every button, input, card, navigation element, video control, question, and interactive component remain tappable/clickable.
* Responsively adapt to an actual mobile viewport when viewed on a phone.
* Maintain the same visual hierarchy, spacing, typography, styling, and overall feel of the current design.

The phone should be represented by the viewport/responsive layout, NOT by an artificial iPhone graphic surrounding the interface.

2. DO NOT CHANGE THE DESIGN INTO A GENERIC MOBILE WEBSITE

Removing the iPhone overlay does NOT mean redesigning the app.

Preserve the existing:

* Visual identity
* Typography
* Colors
* Spacing
* Component styling
* Cards
* Buttons
* Navigation
* Icons
* Content hierarchy
* Overall UX/UI aesthetic

The goal is:

CURRENT DESIGN = THE APP SCREEN

NOT:

CURRENT DESIGN = A PICTURE OF AN APP INSIDE AN IPHONE

The resulting preview should look exactly like the original app design, just without the artificial device frame.

⸻

3. THIS BUILD IS A STYLISTIC TEMPLATE — DO NOT DESTROY THE EXISTING FUNCTIONAL APP

There is an existing functional version of the application in our Lovable/GitHub codebase.

This Figma Make build is intended primarily to provide the visual design system and UI implementation that the existing application can be merged into.

Therefore:

Do not remove, replace, or fundamentally restructure functionality that may already exist in the original application.

Preserve sufficient structure and spacing for the developer to merge the existing functionality back into this design.

Think of this as:

Existing Lovable/GitHub app = functional source of truth

This Figma Make build = visual/style/UX template

The final implementation needs to combine the two rather than allowing this build to overwrite or break the existing application’s functionality.

⸻

4. FIX THE VIDEO + QUESTION LAYOUT

The current build has a major structural problem:

The questions are appearing inside the area where the video is supposed to be displayed.

This is incorrect.

The video and the questions need to be separate components with separate containers.

Video section

Create a dedicated video container with a clearly defined aspect ratio and enough space for the video player.

The video container should contain ONLY:

* Video
* Video controls
* Video-related overlays if they are intentionally part of the design

Do NOT place the questions, answer choices, forms, or questionnaire UI inside the video container.

Question section

Create a separate dedicated section outside/below the video container for the questions.

The question area needs enough room for the developer to implement:

* Question text
* Answer choices
* Buttons
* Radio buttons / checkboxes if required
* Text inputs if required
* Feedback states
* Correct/incorrect states
* Next/previous controls
* Any other existing question functionality

The questions should be fully interactive and tappable independently from the video.

⸻

5. CREATE PROPER LAYOUT SPACE FOR THE DEVELOPER

Do not solve the problem by simply shrinking the questions or forcing everything into the existing video box.

Instead, restructure the page into clear sections:

VIDEO CONTAINER

↓

QUESTION / INTERACTION CONTAINER

↓

ADDITIONAL CONTENT / NAVIGATION AS NEEDED

The layout must have enough vertical and horizontal space for the developer to restore all existing functionality from the Lovable/GitHub version.

Do not use absolute positioning in a way that causes the question interface to overlap the video.

Use a responsive layout structure so that the sections naturally flow and resize based on the viewport.

⸻

6. RESPONSIVE MOBILE BEHAVIOR

The actual app needs to work properly on a phone.

At mobile width:

* The app should occupy the available viewport.
* No iPhone frame should appear.
* No fake device bezel should appear.
* No content should be clipped because of the previous iPhone dimensions.
* The video should scale responsively.
* Questions should remain completely visible and tappable.
* Buttons should have appropriate touch targets.
* Text should remain readable.
* Scrolling should work naturally when the content exceeds the viewport.
* Nothing should overlap or become inaccessible.

The design should also remain usable at larger viewport sizes.

⸻

7. IMPORTANT: DO NOT OVERWRITE FUNCTIONALITY

When preparing this build for GitHub/Lovable integration, prioritize preserving compatibility with the existing code.

Do not:

* Delete existing functionality simply because it is not represented visually.
* Replace functional components with static placeholders.
* Remove existing data structures.
* Remove existing question logic.
* Remove existing video logic.
* Remove navigation logic.
* Hard-code interactions that were previously dynamic.
* Create visual components that prevent the developer from reconnecting the existing functionality.

If something is currently represented only visually, create the appropriate UI space/component structure so the developer can reconnect the existing functionality.

⸻

8. FINAL GOAL

The final result should feel like the real production app, not a design mockup.

I need to be able to open the preview and interact directly with the application screen.

There should be:

NO iPhone overlay.

NO decorative device frame.

NO fake phone shell.

Instead:

JUST THE APP SCREEN + RESPONSIVE INTERACTION.

The existing Lovable/GitHub application should then be able to use this build as its stylistic/UI template without losing its existing functionality.

Most importantly, fix the current video/question layout so that:

VIDEO = its own dedicated container

QUESTIONS = their own dedicated interactive section outside the video

and leave enough room and clean component structure for the developer to merge the existing question, video, navigation, and application logic back into the design.