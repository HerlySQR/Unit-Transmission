**How it works:**

Set a value of type Transmission and use the function create like this:
```java
let curr = new Transmission()
```
Also you can set the target force of the transmission using:
```java
curr.SetTargetForce(force toForce)
//This not assing a force, just move the players of the input to the actually target force, so using a function that creates a force
//in the argument toForce will cause an object leak.
```
You can also save an integer to use during the transmission using:
```java
curr.setData(data)
// To recover the data use:
curr.getData()
```
If you wanna add or remove a player or a force to the target force just use ```getTargetForce``` and the normal functions addPlayer, removePlayer and the added addForce, removeForce
To reduce the process use:
```java
let curr = new Transmission(force toForce, integer data)
```
Then to add a dialogue line just do:
```java
curr.addLine(unit whichUnit, playercolor color, string unitName, sound soundHandle, string message, TTime timeType, real timeVal, boolean wait)
```
*whichUnit:* The unit who do the dialogue line.<br />
*color:* The color of the unit in the dialogue (if you set this value to null so then, with the StoreUnitColor library the unit will have their actual color instead their owner color).<br />
*unitName:* The name of that unit.<br />
*soundHandle:* The sound that will be played during the dialogue line.<br />
*message:* what the unit will say.<br />
*timeType:* Is like the GUI function, you can use TTime.ADD, TTime.SET, TTime.SUB, another value will make the wait time to 0.<br />
*timeVal:* Is like the GUI function, the normal duration of the transmission is the duration of the sound and you can edit the value depending of this and the previous value.<br />
*wait:* Is like the GUI function, if this is true the next transmission will be wait the time depending of the previous two values.<br />

In case you don't wanna set a unit, but instead a unit-type you can use ```curr.addLinebyId``` instead (default color BLACK)
    
Also you can add actions between the lines using the function:
```java
curr.AddAction(real delay, TransmissionCallback cb)
```
*delay:* If you wanna add a delay to the next action or line.<br />
*actions:* The function that will run.<br />
          
If you wanna the action run in the exact moment a line is happening just set the delay to 0.00 and put it right before the line.
If you wanna just add an extra delay just put ```null``` in the actions part
        
I have to say this steps are assinged an index by the order they were declared that is the number of the step.
    
You can also add an action to the end of the transmission (this function will run yes or yes even if you skip the transmission) using:
```java
curr.addEnd(TransmissionCallback cb)
```
Where you can use the values ```curr = Transmission.get()``` to reffer to the actual instance, and with that use ```curr.getData()``` if you stored one and ```curr.isSkipped()```
to check if the cinematic was skipped, but if was skipped in the last line this value will remain false.
            
After set the lines call the function
```java
curr.start()
```
To start the transmission, and to skip the transmission (the end function will run anyway) just press the ESC button,
(this will skip all the transmissions where the player is) but if the implied force has more than one player the process will continue
to the rest of players on that force (the target force is not empty). The transmission will end only if all the implied players skip the transmission (So if you use "All players"
probably you never won't skip the transmission) but you can use the ```player.getForce()``` to have a force just for 1 player (Don't destroy those forces).
    
If you wanna you can also pause the transmission using:
```java
curr.stop()
// or:
curr.stop(real delay) //If you wanna the transmission resume itself after a while
```
And to resume manually use:
```java
curr.resume()
Note: The transsmision and its elements are not stored after start it, they are destroyed once the transmission ends.
```

Other members:

```force getOriginalTargetForce()```, if you edited the target force, you can have the original force with this.
```player getTargetPlayer()```, if the transmission is just for one player you can use this instead, in other case this will return the last added player. (I don't know the order)
```int getStepNumber()```, is the number of the step that is running (line or actions).
```boolean isPaused()```, returns if the transmission is paused.
```TransmissionElements getElements()```, to reffer to the elements instance of the actual step.
``` int size()```, the number of steps has the transmission.

Some other functions:
```java
addSceneActions(TransmissionCallback cb) returns TransmissionCallback
```
If you wanna add an action that runs everytime an step (line or action except the end) is runned, this also can be a replace of addAction in some
cases if you compare the number of the step with StepNumber, to know if is a line or action use the member boolean IsLine
This also runs before the action or line is runned, so you can use it to edit the current step.
```java
removeSceneActions(TransmissionCallback cb)
```
This remove the actions that you added before.
                
If you wanna set the elements by separated you can use the member
```java
addStep(string whattype) returns TransmissionElements
```
This add an step that also have an index by the order is placed, the input only can be ```"line"``` and ```"action"```
```java
insertStep(int stepnumber, string whattype) returns TransmissionElements
```
This add an step but the index is set manually, if there are steps that were set after this and after the transmission was started
(including that was had that stepnumber) those index will be increased by 1 to not overwrite.
```java
removeStep(int stepnumber)
removeStep()
```
This remove an step using the number of the step, in this case the stepnumber of the next steps will be decreased by 1, not assign an number will remove the last added.
            
After add the step the returned value have their own members

```java
setTalker(unit talker) //This is the unit who will be marked during the transmission (not who will apear in the transmission).
setTalkerId(integer talkerid)
setColor(playercolor color)
setName(string name)
setSound(sound mysound)
setText(string text)
setDuration(real duration) // If is a line
setTimeType(TTime timetype)
setWillWait(boolean wait)
setDelay(real delay) // If is a step

int getNumberStep()

setActions(LinkedList<TransmissionCallback> actions)  //This replace all the actions
addActions(TransmissionCallback actions))  //This add actions without affecting the added actions
getActions() returns LinkedList<TransmissionCallback>
```
But if you wanna use this members using the number of the step use their "ByIndex" methods (these are members of Transmission not of TransmissionElements)
You have to do:
```java
curr.set$Name of the member$ByIndex(int stepnumber, $type of the member$ value)
value = curr.get$Name of the member$ByIndex(int stepnumber) (Except WillWait, the method is waiting)
```
Example:
```java
let curr = new Transmission()
curr..addStep("line") // is the step 1
    ..addStep("action") // is the step 2
    ..setTalkerByIndex(1, <your unit>)
    ..setActionsByIndex(2, <your actions>)
// A replace of:
let curr = new Transmission()
curr.addStep("line")
    ..setUnit(<your unit>)
curr.addStep("action")
    ..setActions(<your actions>)
```
But if you wanna save the number of lines you can set default values (before than create the steps)
```java
unit defTalker
int defTalkerId
playercolor defColor
string defName
sound defSound
string defText
real defDuration
int defTimeType
boolean defWillWait
boolean defDelay
```

