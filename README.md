# ActionMover

This might become an addon for WoW to move all actions from one bar to another bar. For now, I'm using it for taking notes.

## Mappings

This section is weird, and I'll explain after this table.

| Action Bar | Default UI | ElvUI equivalent | Notes              |
|------------|------------|------------------|--------------------|
| 1          | 1-12       | Bar 1            |                    |
| 2          | 61-72      | Bar 6            |                    |
| 3          | 49-60      | Bar 5            |                    |
| 4          | 25-36      | Bar 3            |                    |
| 5          | 37-48      | Bar 4            |                    |
| 6          | 145-156    | Bar 13           |                    |
| 7          | 157-168    | Bar 14           | ElvUI Stance/Forms |
| 8          | 169-180    | Bar 15           |                    |
| 9          | n/a        | n/a              | only in ElvUI      |
| 10         | n/a        | n/a              | only in ElvUI      |
| 11         | n/a        | n/a              | doesn't exist      |
| 12         | n/a        | n/a              | doesn't exist      |
| 13         | n/a        | n/a              | only in ElvUI      |
| 14         | n/a        | n/a              | only in ElvUI      |
| 15         | n/a        | n/a              | only in ElvUI      |

### Mappings Overview

In the default UI, the buttons in action bar 1 have numbers 1-12. While the buttons in action bar 2 have numbers 61-72.

So if you are using the default UI and want a macro that picks up the action from action bar 1 button 1 and places it on action bar 2 button 1, you'd run this command in your chat window:

`/run PickupAction(1) PlaceAction(61) ClearCursor()`

This picks up the action from button 1 and places it into button 61, overriding whatever was in button 61 (action bar 2 button 1).

Similarly, if you wanted to move the *last* action from bar 4 to the *second* action in bar 6, you'd run:

`/run PickupAction(36) PlaceAction(146) ClearCursor()`

This is because the twelve buttons in bar 4 are equivalent to buttons 24-36 and the twelve buttons in bar 6 are equivalent to 145-156.

### Mappings Mass Move

If you want to enter a single command that moves *all* actions from one bar to another, you'd use a *for* loop.

In this example, we move all actions from bar 1 to bar 2:

`/run local dst=61; for src=1,12 do PickupAction(src) PlaceAction (dst) ClearCursor() dst=dst+1;end`

That command says:

- For each button from 1 to 12, move the action to button 61 and increment each time. So 1 goes to 61, 2 goes to 62, 3 goes to 63, etc.
- The net result is that all twelve actions from bar 1 get moved to bar 2.

Here's another example moving all actions from bar 8 to bar 5:

`/run local dst=37; for src=169,180 do PickupAction(src) PlaceAction (dst) ClearCursor() dst=dst+1;end`

### ElvUI Mappings

This is where things get *ridiculous*. If you're using the default UI and place six action bars at the bottom of your screen, counting up from the bottom (so bar 6 is the top bar) and you switch to ElvUI with the same placement (bars 1 through 6 going up from the bottom of your screen), you'd *expect* the same spells to be there. **But this is only true for action bar 1**.

Unfortunately, the buttons you had on bar 3 on the default UI will end up on bar 4 of ElvUI. And the buttons you had on bar 6 will end up on bar 2 of ElvUI.

So when migrating from default to ElvUI, you can't simply place action bar 1 where action bar 1 was, action bar 4 where action bar 4 was, and so on.

If you don't want to move actions when migrating from default to ElvUI, then you'll have to place ElvUI bar 6 where default bar 2 was and ElvUI bar 13 where default bar 6 was. And so on.

Now let's say you are a stickler for meaningless numbers and want to put ElvUI bar 2 *exactly* where default bar 2 was and retain the same actions. Well, you know there is a mismatch. You know that the actions you had on default bar 2 are now on ElvUI bar 6.

In that scenario, you might *think* you can simply run the "bar 6 to bar 2" loop command as follows:

`/run local dst=61; for src=145,156 do PickupAction(src) PlaceAction (dst) ClearCursor() dst=dst+1;end`

You'd think that would work because it works in the default UI to move actions from buttons 145-156 (bar 6 buttons 1-12) to 61-72 (bar 2 buttons 1-12).

But it doesn't! Because ElvUI changes the action numbers. And the `/dump GetMouseFocus().action` command doesn't work in ElvUI.

I did some manually testing by populating an ElvUI bar, writing down which actions I placed on the bar, switching to default UI, and then grabbing the number of the action for whatever bar that bar moved to.

For example, I placed "Guild Mail" on ElvUI bar 6 button 1. Then I switched to the default UI and ran the command to get the value of Guild Mail (which now was on default bar 2 button 1). It is 61 as expected, because default bar 2 always starts with 61.

But if you run the same command when ElvUI is loaded, it doesn't move to action 61. It goes somewhere else. And I thought...okay, so I just need to find the first number of each destination and count up 12 from there. *But it doesn't work like that.* Buttons 1-12 for a bar in default UI might correspond to buttons 1-6 in one ElvUI bar and buttons 7-12 in another ElvUI bar! It's absolute madness!

It was at this point in the project that I gave up trying to script my migration from ElvUI to default UI.

## Development Notes

### Get button number while hovering

`/dump GetMouseFocus().action`

### Enable debugging

`/console scriptErrors 1`

### Get all action slots

`/run for i = 1, 120 do local x = GetActionTexture(i) if x then print("Slot " .. i .. ":", GetActionText(i), x) end end`

### Place a specific spell value to bar 2 button 1

`/run PickupSpell("100") PlaceAction (61) ClearCursor()`

### Move an action (bar 5 button 1 to bar 2 button 1)

`/run PickupAction(37) PlaceAction(61) ClearCursor()`

### Remove bar 3 button 1 from action bar

`/run PickupAction(49) PutItemInBackpack() ClearCursor() end`

## LIcense

Distributed under the MIT License. See LICENSE for more information.
