# Week 10 -- Studio Time

This week there is no practical, and you will instead be making a start on your assignment.

To get started on your assignment, please [use the following link to open the GitHub classroom page](https://classroom.github.com/a/pce0BnjC) (or check out the section **Assessment: Game Prototype** on iLearn)

```

```

We were unable to finish writing the prac last week in time (even after an all-nighter), so one activity was missed. A lot of the context that was supposed to be given had to be cut, but that information still exists in the week 8 lecture - it is *highly* recommended to watch lecture on echo if you have not seen it (if not, at least watch it before continuing with this activity).

This prac includes that missing activity from last week (Health Bar, as opposed to simply text), alongside some further reading for what is required on UI Anchors and Pivots.


|   |                                                                                                  |   |
| --- | -------------------------------------------------------------------------------------------------- | --- |
|   | The attached activity is NOT required for your assignment submission, and is completely optional |   |
|   |                                                                                                  |   |

## Further Reading on Anchors and Pivots

At the end of the last lesson, we asked you to set the following anchor on the Health text.

<img src="PracResources\images\21_HealthTextTweaks.png">

After setting your Health text, make sure you do the following:

* Ensure the **Anchor** is set to the top left
* Move the blue [T] (the **Pivot**) from the middle of the text to some place sensible (the left side, the top left corner) - this is finicky to do in the scene view, it is recommended to use the fields in the **Inspector** instead
* Give the text some an appropriate offset by changing the `Pos X` and `Pos Y` in the object's **Rect Transform** component.

Ideally, your text element should look something like this, although again, keep in mind I used 999 as my health; your health number might be different

<img src="PracResources\images\01_PositionHealthText.png">

If for whatever reason the yellow box (your TMP text box) does not match your position, or is simply not where you want it to be, in the `Text Mesh Pro - Text (UI)` component, scroll down to the **Extra Settings** group. Click it to expand its contents, and set the `Margins` to 0 (doing this should snap it to the center of your rect transform)

<img src="PracResources\images\02_ResetMargins.png">

Again, ensure you check your text in separate aspect ratios to verify it is working as intended (by using the dropdown arrow in the **Game** window)

## Creating a UI Manager

While it made sense to update the UI text from our player, this isn't exactly ideal, since we should be separating the UI logic from game logic - you can imagine how important this separation would be when working in teams, or when scaling up your game.

Make a new empty game object called "`UI Manager`", and give it a `Script Machine` component. Once you've done this, make it a child of the `Canvas` object (ideally, it should always be the top-most object as well), make a new script for it (call it something sensible, like `UI_Manager`).

Select your `Player` and remove the variable giving a reference to the `HealthText` object, and Cut & Paste your Set Health text nodes inside the script in the `UI Manager`, and assign them to start `OnUpdate` - note that even though this would be running our code every frame, running update on UI is typically cheap computationally, and the alternatives wouldn't provide much benefit in terms of performance (but would increase complexity drastically).

After you've done that, add two new variables to the  `UI Manager` that give a reference to the Health Text object and the `Player` object. Once you've created these variables, check your `UI_Manager` script to ensure that you're getting the correct health variable from your player (you will need to use two `GetVariables` together in order to achieve this, with the `player` variable being passed along as an object reference).

Once you've finished, press **Play** and test your code. For reference, your UI manager shouldn't be too different from this example (note that running this with `OnStart` could be seen as redundant, but verifying the starting state of an element can be a smart approach, as you're verifying the state immediately on startup):

<img src="PracResources\images\03_TextUIManager.png">

### Health Bar

We have a raw health number, which is fine for a lot of cases, but let's create something a bit more modern and implement a health bar. To do this in Unity, the simplest way is to instead make a `Slider` object (think like a volume bar) and to tweak it slightly - this is more common than you might expect at first. You can make a slider by right clicking in the **Hierarchy** and selecting `UI > Slider`

First things first, let's detach the knob from our slider. Select our new `Slider` object in the **Hierarchy**, navigate to the `Slider` component, and click the object picker button beside the `Handle Rect` field. You should see a popup box appear with a few options, and you want to select `None`. Once you've done that, delete the child object in your Slider called `Handle` (the knob sprite) & the `Handle Slide Area` object (the bounds for the knob sprite). You should have something like this when you're done:

<img src="PracResources\images\04_KnobRemoved.png">

My slider might look slightly different than yours, as I've already gone ahead and changed the `Value` of my slider to something a bit easier for testing. When a slider is made, by default, their value is set to 0, but it is recommended you change this for the next step. We'll change the max value later, but for now just set it to an appropriate number, like 0.5 or 0.75.

At this point, we're going to go ahead and make this look more like a health bar::

* In our Slider's `Rect Transform` component, change `Width` to around `500`
* In our Slider's `Rect Transform` component, change `Height` to around `80`
* In our Slider's `Slider` component, Set the `Transition` to be `None` (we don't need a transition)
* In the `Background` child, in the `Image` component, click the `Color` field to bring up the colour wheel, and set the colour something transparent - this could be a faint charcoal color. I used a transparent pink colour.
  * Note that on the color picker wheel A affects the alpha/transparency of the color - ask your TA if you're unsure about this
* Locate the child of `Fill Area` -> `Fill`, and set the colour in the `Image` component to be a more solid colour. You could make this a green or red health bar, whatever you want.

Move your health bar in to roughly the same position as your health text, and remember to verify the anchor, pivot and offset of your health bar. You may also want to check it's positioning in your game view, so it would make sense to flick back and forth between **Game** and **Scene** view, or have the two views up side by side. Once you're happy with the positioning, test that it scales correctly with different screen sizes/aspect ratios (doing this constantly while making UI is a good habit to learn).

For demonstration purposes I went with a solid green health bar, with the same color (but more transparent) as the background.

<img src="PracResources\images\05_GreenBar.png">

You can go ahead and test how it'll look by dragging the `Value` bar (in our `Slider` component) back and forth. You'll notice that it's a bit off - when the health is at 100%, it doesn't go all the way. When it's at 0%, it looks like there is some health left over. Our fill areas are slightly offset in both directions to cater for a knob (otherwise the knob would be out of position). However, since we aren't using a knob, we want this removed.

The fix is quite easy. You can resize your objects manually using the **Rect** tool, but it's easier to just do the following quickly:

* In the `Fill Area` object, set `Left` and `Right` to `0`
* In the `Fill` object, set `Left` and `Right` to `0`

Once you've done that, all that's left is to hook it up. The setup is similar to the text, but we're going to make testing a bit more automated. In our `UI Manager`, create a new variable for the HealthBar (`healthBar` is a reasonable name), and give it the Type: `Slider`. Give it the reference for our newly created slider. Open up your visual script graph, and delete the functionality for player text if you wish, but keep the functionality that gets the `Player` health. Either way, make the following two nodes:

* Slider: Set Max Value
* Slider: Set Value

<img src="PracResources\images\06_SliderSetMax.png">

We're going to use the `Slider Set Max Value` node to set the 100% value of our slider on startup. Since we know the player is starting on full health, we can simply get the current health of the player as the game starts.

Now, we could simply set this manually in our `Slider` component, but if our starting health changes during testing, we'd have to go back and make sure we update this; it always makes sense to automate things like these to reduce the potential for bugs.

<img src="PracResources\images\07_SliderSetVal.png">

This `Slider: Set Value` node will simply pass the number value of our health to our slider, and functions similarly to our SetTextHealth node. Obviously, the major difference between this and our text is what Unity does in the backend (it takes the value and updates the positions of the background and fill of the health bar accordingly).

Look at the inputs for these two slider nodes, and have a go at figuring out the logic by yourself. It is pretty straightforward and shouldn't take you too long. Of course, a solution is provided below if you get stuck.

Once you've think you've got an answer, press **Play** and check that everything works. It may be ideal to change your health to be lower during testing (I set my player health from 999 to 99, for example). It's a good thing we automated the check for setting the health max value.


|   | A Bug?                                                                                                                                                                                                                                                                                                                                                                                      |   |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | Keen-eyed students might notice at this point that there was arguably a bug in last week's material. Technically, the player health can go below 0. While this is something invisible to us unless we were actively looking at variables (and is fine as an oversight when developing), you should consider edge cases like these when testing the game you are making for your assignment. |   |
|   |                                                                                                                                                                                                                                                                                                                                                                                             |   |

To end the day, it wouldn't hurt to resize your UI a few times just to triple check it's working.

<img src="PracResources\images\08_SliderUIManager.png">

While there are no challenges this week, these are the challenges that were left out of last week's lesson. Again though, this is not at all mandatory.

## Additional Content - Create a "Juicy" Health Bar

Add some polish to your health bar - think about industry norms, and what some of your favorite games do with their health bars. Fighting games for example have "white health", temporary health that appears on your health bar before being counted. Other games like Pokemon slowly change the health over time. Both in theory are similar, funnily enough.

### A Health Bar That Shows Immediate Damage Taken Before Disappearing

To make a health bar that shows immediate damage taken, before disappearing (like some fighting games), you would likely need to count previous health (before damage), current/remaining health (after damage), as well as a timer that counts when to set this damage. In theory this is quite simple, but it may take some time to tune

### A Health Bar That Slowly Ticks Down

A "[Lerp](https://docs.unity3d.com/Packages/com.unity.visualeffectgraph@11.0/manual/Operator-Lerp.html)", or something similar could be used to set the previous health (before damage), to the new health (after damage). Essentially you should increment the health loss over time. Consider this though:

* Lerp requires the input of `S` to be a value from 0 - 1. 0 would essentially be 0% of X -> Y (i.e. exactly X), and 1 would be 100% (i.e. exactly Y)
  * S being 0.5 would therefore mean it exactly the midpoint between X & & (e.g. if we had X: 1, Y: 2, S: 0.5, this would result in the Lerp output giving 1.5)
* This means that we would have to set S as a value relative to our timer, since S can't be greater than 1. You may have to do some research to find out how others have solved this problem before
  * Ask on the forums if you cannot find a good answer for this, there is a simple solution
