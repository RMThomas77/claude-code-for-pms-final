# 05 · Super Speed — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. You've spent three sessions finding out what went
wrong: the two piles of feedback that didn't agree, the numbers that
hid four people inside an average, and the code that settled it —
the shorter timeout is what did it.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

Okay, let's start over.  I want to build a brief to provide to Helen, hold on any build, I want to focus on the brief first and refine that.  I want it clear and concise based on our findings this week.

### 2.

Let's focus this brief around Vesper as an example.

### 3.

Is there any way to tell if the longer ping times (90 versus 60) truly reduced the response time?  Why was it necessary to lower that?

### 4.

Yes, I think the original points in the brief are valid, and should be tried first, but we should have a dev further look into the 90 to 60 change to see if it made any difference in response time (getting to a responder that accepted quicker).

### 5.

Can we get this down to a one page brief?

### 6.

I don't see a clear "here is what we should fix"

### 7.

let's focus on the first 2, and hold the third that we could do after we fix it for the responders.

### 8.

Add a one line follow up to address the Console as next step.

### 9.

In this prototype I feel like Nightwell routinely declined/missed, but his place never dropped, I feel like maybe the location weight is actually causing more impact than intended.

### 10.

I'd like to go back to the assumed solution, and look at where Vesper truly started falling.  For example, if we kept the new weight for distance, but pint the ping rate back to 90.  Let's reset the prototype for this scenario, and set all their numbers back to what they were before 4.2 was released, I want to see where Vesper would be in the ranks.

### 11.

the prototype doesn't seem to be updating the weights after each run, only if I move it a week,

### 12.

can you update the pings / week as well?

### 13.

I'd like the option to choose who declines/misses/takes each call so I can get a better option.

### 14.

Okay, I want you to run some sample data, if we leave as is today, what would it take for Vesper to recover in the simulator?

### 15.

In the prototype I need a reset option, for everything

### 16.

let's go back to the initial prototype, with just the two fixes, now that we fixed the view and the ability to set the When Asked, I'd like to test those again.

### 17.

I think we need the recovery time to be quicker, a way to reset to neutral by responder, and keep the two fixes.

### 18.

and update my brief with those changes as well as the prototype.

### 19.

Update the prototype to have an Admin option to set responder back to neutral (by responder)

### 20.

I'd like a different layout, let's put the fixes at the top as well as the recovery speed and and weeks after the fixes ship.

### 21.

I want to do one more test, if we alter the weight for distance for a fix, not all the way back to what it was, but slightly less than what it is now, reset all responders back to neutral, what would be the pace for Vesper to get any calls for Old Town?

### 22.

I'm trying to get to a point where the callout is more evenly set across the responders for that area, it's reasonable to ask the closest first, but that can also lead to burn out, it feels like it should share the calllouts if they are within a reasonable distance.

### 23.

Okay, let's keep the two fixes, but add in 3 follow up notes 1. possibly the need to revert the ping time, 2. possibly need to revert the distance weight, and keep the 3. Responder console.  I also think we need to follow up with surveys to the responders to see if they feel like they are being pinged more often.
