# Salesforce FLOW IoT Example Collection

Hopefully this becomes just a litany of fun little whatnots for ya. Otherwise, I mean not many people follow me so no harm no foul.

Goal here is to provide you with a somewhat bare bones / baseline IoT 'implementation'. We've a Platform Event, a Custom Object to represent 'exceptions', and we're using Cases/Assets to handle everything else. With that, we're going to group in a few Flows in order to represent a very simple set of common IOT signal style usecases.

#### Few base ideas of how we're handling this - 
* We're going to use Platform Events (Specifically Device_Signal__e) as a method of ingesting information from the faux devices. This would let us easily test this using a quick Heroku app + Loader.io if we wanted to, which we'll get to in a future episode hopefully.
* We will assume that storage of *all* signals emitted are 'somewhere else'. That said, we still want to have local storage of important signals, so we're using an SObject called Device_Exception__c. In the future, we could also add in either BigObjects, or an external store with Salesforce Connect + External Services, but let's get the first thing off the ground.
* So Flows can't directly be invoked from Events - what we'll do is use a Process Builder in order to invoke the Flows needed. If you wanted to go bigger you could also probably toss in some Apex to dynamically call, but baby steps yeah. This gives us a fairly simple initial stage gating to decide what to do. In order to keep sense of where logic lives, we'll use a single Process to gate only on Type (ALARM, WARNING, INFO), and then let Flow do the additional logic in deciding what to do. Any branching within a Flow will be in subflows to try to keep things as granular as we can.

## Example Flows

#### Simple Case Alarm Direct Call
Ok, so this is a simple one. Basically if a device signal comes in with the signal type of 'ALARM', we need to associate the signal to an asset(device) record, and create a case for it for someone to deal with. We'll also create that exception record and tie it to the case so the agent knows what's up.
#### Multiple Warnings Within Time Period
Getting a bit more fun. If we get a WARN signal type, we don't want to necessarily create a case. We've created a requirement that 3 WARN signals within a 1 hour span means we need to investigate. So we'll use this to show using the Database as the state for the flow, and simply check the status of the device as it stands. We'll store every WARN as an exception event to simplify things, which would be fairly easy to run a scheduled process to clean out any not associated with a case nightly if you wanted to keep storage costs down.
#### (TBD) Multiple Signals Within A Single Case
Alright, so let's say there's a known pattern of signals that are invariably linked. The simplest to understand would be a server reset. You get the INFO signal that a restart has occurred, followed by INFO signals telling you that individual services are restarting, ending with an INFO signal telling you that the system status is live again. We need a system to capture ALL of these signals, and logically group them under a single case to be reviewed by an agent. We'll do this using the Pause/Resume feature within Flow as a means to track state. It's important to note - you do risk limits with this, you can only have some 50k interviews paused at a time, but we obv won't hit that here.
#### (TBD) Rapid Escalation Of Measures
We've two generic "Measure" fields on our Event. What we want to do is identify thresholds for Measure 1, which is a fairly simplistic exercise, and then 
for Measure 2, we want to detect rapid escalation in order to pro-actively identify a problem.
#### (If there's time) Keep-alive signal tracking
TBD

## Dev, Build and Test

## Resources

## Description of Files and Directories

## Issues
