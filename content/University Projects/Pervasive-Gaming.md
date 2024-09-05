+++
title = 'Pervasive Gaming'
date = 2024-09-05T11:50:57+01:00
draft = true
+++


# University Module - Pervasive Gaming

[Github Repo](https://github.com/AgentArachnid66/PVG)

{{< youtube Hzj0wWWOu5A>}}

This unit was one of my favourites at University, as it was a challenge to create a game without the traditional inputs that are the norm in the industry. I had set myself a self imposed goal of developing the game without using mouse or keyboard or gamepad as inputs, but instead use Hand Gestures using UltraLeap, alongside Voice Recognition using the built in libraries as well eye tracking as a stretch goal. 

I managed to fully utilise all 3 technologies to make a tech demo that still impresses. 

I faced a major roadblock as I couldn't get the keywords system to work so with less than 24 hours to go, I created my own basic version of it using dictionaries.

```csharp
 // This Function Processes a Sentence the Player Says
 void ProcessPhrase(string word)
    {
        // Refers to the Mode and Hand in the Phrase
        Hand hand = Hand.Both;
        Mode mode = Mode.Hand;
        bool closing = false;

        // Debug to Pause the editor
        if (word.Contains("break"))
        {
           // Debug.Break();
        }
        else if (word.Contains("quit game"))
        {
            Application.Quit();
        }
        else
        {
            // Creates an array of words to process then iterates through them
            string[] words = word.Split(' ');
            foreach (var w in words)
            {
                Mode testMode;
                Hand testHand;
                // Attempts to get the value from the Mode Dictionary from a word
                // the player says
                if (modeDict.TryGetValue(w, out testMode) && !closing)
                {
                    mode = testMode;
                    // If closing a general mode then this should keep the mode in question
                    // from overwritting it
                    closing = mode == Mode.Hand;
                }
                // Attempts to get the value from the Hand Dictionary from a word
                // the player says
                if (handDict.TryGetValue(w, out testHand))
                {
                    hand = testHand;
                }
            }

            // Invokes a Unity Event that takes in the Enum Values so that the listeners
            // can handle the heavy lifting
            customEvents.switchMode.Invoke(mode, hand);
            
            Debug.LogError("Switched To: " + mode.ToString() + " On The: " + hand.ToString() + " Hand");
        }
    }
```

The Hand Tracking was accomplished using the UltraLeap device and a Unity Package. However, I extended this functionality to map the location data to the drone "hands" that I use with the Lasers. 

```csharp
    // Maps the hand data from teh Leap Motion to GameObjects
    void MapHands(bool isLeft, Leap.Vector localLocation, Leap.Vector orientation)
    {
        // Local variable to hold the correct location
       Vector3 pos = MapHand(localLocation);
       
       // Selects the correct hand to affect
        if (isLeft)
        {
            leftHand.transform.position = MapHand(localLocation);
            leftHand.transform.rotation = Quaternion.LookRotation(CustomUtility.LeapMapOrientation(orientation));
        }
        else
        {
            rightHand.transform.position = MapHand(localLocation);
            rightHand.transform.rotation = Quaternion.LookRotation(CustomUtility.LeapMapOrientation(orientation));
        }
        
        // Passes this data onwards to the relevant classes
        CustomEvents.CustomEventsInstance.UpdateLaser.Invoke(isLeft, pos, CustomUtility.LeapMapOrientation(orientation));
    }

    Vector3 MapHand(Vector location)
    {
        /* Steps taken in this function:
            * Convert the Leap Data from their coordinate system to Unity's
            * Multiply the data by the distance to put the hands a set distance away from the player
            * Add an offset to control where they appear relative to the drone
            * Rotate this by the rotation of the drone so they appear in front of the drone at all times
            * Finally translate by the position of the drone to convert from local to world space
         */
        Vector3 newPos = (_drone.rotation * ((CustomUtility.LeapMapHands(location) * distance) + offset)) + transform.position;
        return newPos;
    }
```

The Custom Utility Class is a static class I made to handle a lot of the Leap functionality. Here is the definition of the LeapMapHands:
```csharp
// This functions is useful for converting Leap Vectors into Unity's Vector3. I've 
// changed it's output to work with the mapping functionality required of it
public static Vector3 LeapMapHands(Vector vector)
{
    return new Vector3(vector.x/2.5f, vector.y/5f, -vector.z)/1000f;
}
```

I used the hands to make the digging lasers and as shown in the video, you can combine them to make one bigger laser which power outputs is relational to the distance between your hands. 

```csharp
// Check if the player has both hands as lasers
if (hands == Hand.Both)
{
    // Add a scalar * direction of the hands to the position of the hand to each one
    
    // If the distance between these points is less than the distance between the hands
    // then the hands are facing each other .
    
    // Else the hands are in an incorrect orientation.

    // If the dot is close to -1 then the hands are facing each other
    _combine = Vector3.Dot(leftHand.orientation.normalized, rightHand.orientation.normalized) <= -0.75;
    
    combineTest.gameObject.SetActive(_combine);
                
```
                
                
```csharp

if (_combine)
{
    // If we want to combine, then we can calculate the power using the distance between the hands
    _handDist = leftHand.location - rightHand.location;
    // The output of the combined laser is inversely proportional to the magnitude of the hand Dist
    // vector so that the smaller the distance, the bigger the output
    currOutput = 1 / _handDist.magnitude;

    // The direction vector is the average direction vector from the rig to each hand
    _laserDir[0] = ((leftHand.location - rig.position) + (rightHand.location - rig.position)) / 2;
    // Directs the laser downwards as otherwise it wouldn't hit anything
    // as the hands are always above the rig
    _laserDir[0].y *= -1;

    // Starting point of the laser is the midpoint between the two 
    // position vectors
    _laserPos[0] = (leftHand.location + rightHand.location) / 2;

    // Finally Update the lasers VFX
    combineTest.SetStartAndEndPoints(_laserPos[0], _laserPos[0]+_laserDir[0]*500f);

    foreach (VolumetricLineBehavior hitTest in hitTests)
    {
        hitTest.gameObject.SetActive(false);
    }

}
```

```csharp
else
{
    // If we are not combining, then iterate over the hands and set the appropriate values
    for (int i = 0; i < _laserDir.Length; i++)
    {
        currOutput = maxBurnout / 2;

        _laserDir[i] = i == 0 ? leftHand.orientation : rightHand.orientation;
        _laserPos[i] = i == 0 ? leftHand.location : rightHand.location;

        hitTests[i].transform.position = _laserPos[i];
        hitTests[i].SetStartAndEndPoints(Vector3.zero, _laserDir[i] * 500f);
        hitTests[i].gameObject.SetActive(true);
    }
}
}
```


The Eye tracking was done using a Unity Package that came with the Tobii Eyetracking.