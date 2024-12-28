### Chapter-4

---

This script, written in C#, is for Unity and handles a gameplay mechanic where a target is "killed" after being focused on for a certain amount of time. Here's a detailed breakdown of each part:

---

### *Namespaces*
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
```

- **using System.Collections**: Provides support for non-generic collections like ArrayList.
- **using System.Collections.Generic**: Provides support for generic collections like List<T>.
- **using UnityEngine**: Gives access to Unity's classes, such as GameObject, MonoBehaviour, ParticleSystem, etc.

---

### *Class Definition*
```csharp
public class KillTarget : MonoBehaviour
```

- **public**: The class is accessible from other scripts.
- **class KillTarget**: The script's name.
- **MonoBehaviour**: A Unity-specific base class that allows the script to be attached to GameObjects and utilize Unity's lifecycle methods like Start() and Update().

---

### *Variables*
```csharp
public GameObject target;
public ParticleSystem hitEffect;
public GameObject killEffect;
public float timeToSelect = 3.0f;
public int score;

private float countDown;
```

1. **public GameObject target**: A reference to the target GameObject.
2. **public ParticleSystem hitEffect**: A particle effect that plays when the target is being "focused on."
3. **public GameObject killEffect**: A GameObject, likely a particle or visual effect, spawned when the target is "killed."
4. **public float timeToSelect = 3.0f;**: The time (in seconds) the player must focus on the target to "kill" it.
5. **public int score**: Tracks the player's score.
6. **private float countDown**: A private timer counting down until the target is "killed."

---

### **Start Method**
```csharp
void Start()
{
    score = 0;
    countDown = timeToSelect;
}
```

- *Purpose*: Initializes variables when the script starts.
1. **score = 0;**: Sets the score to 0 at the beginning.
2. **countDown = timeToSelect;**: Sets the countdown timer to the value of timeToSelect (3 seconds by default).

---

### **Update Method**
```csharp
void Update()
```

- *Purpose*: Runs every frame to handle gameplay logic.

#### 1. *Camera and Raycast Setup*
```csharp
Transform camera = Camera.main.transform;
Ray ray = new Ray(camera.position, camera.rotation * Vector3.forward);
RaycastHit hit;
```

- **Camera.main.transform**: Gets the main camera's position and rotation.
- **new Ray(camera.position, camera.rotation * Vector3.forward)**: Creates a ray originating from the camera and pointing forward.
- **RaycastHit hit;**: Stores information about what the ray hits.

#### 2. *Checking for a Target*
```csharp
if (Physics.Raycast(ray, out hit) && (hit.collider.gameObject == target))
```

- **Physics.Raycast(ray, out hit)**: Checks if the ray intersects with any objects in the scene and stores the hit information in hit.
- **hit.collider.gameObject == target**: Verifies that the hit object is the intended target.

#### 3. *On Target*
```csharp
if (countDown > 0f)
{
    countDown -= Time.deltaTime;

    hitEffect.transform.position = hit.point;
    hitEffect.Play();
}
```

- **countDown > 0f**: Ensures the countdown hasn't reached zero.
- **countDown -= Time.deltaTime;**: Reduces the countdown by the time elapsed since the last frame.
- **hitEffect.transform.position = hit.point;**: Moves the hit effect to the exact point where the ray hit the target.
- **hitEffect.Play();**: Plays the particle effect.

#### 4. *Target "Killed"*
```csharp
else
{
    Instantiate(killEffect, target.transform.position, target.transform.rotation);
    score += 1;
    countDown = timeToSelect;
    SetRandomPosition();
}
```

- **Instantiate(killEffect, target.transform.position, target.transform.rotation)**: Spawns the kill effect at the target's position and rotation.
- **score += 1;**: Increments the player's score.
- **countDown = timeToSelect;**: Resets the countdown timer.
- **SetRandomPosition();**: Moves the target to a new random position.

#### 5. *Missed Target*
```csharp
else
{
    countDown = timeToSelect;
    hitEffect.Stop();
}
```

- *Purpose*: Resets the countdown timer and stops the hit effect if the ray no longer hits the target.

---

### **SetRandomPosition Method**
```csharp
void SetRandomPosition()
{
    float x = Random.Range(-5.0f, 5.0f);
    float z = Random.Range(-5.0f, 5.0f);
    target.transform.position = new Vector3(x, 0f, z);
}
```

- *Purpose*: Moves the target to a random position within a defined range.
1. **Random.Range(-5.0f, 5.0f)**: Generates a random number between -5 and 5 for the x and z coordinates.
2. **target.transform.position = new Vector3(x, 0f, z);**: Sets the target's position to the new random coordinates.

---

### *Summary*
This script:
1. Detects whether the player is focusing on a target using a raycast.
2. Plays a particle effect (hitEffect) while the player is focusing on the target.
3. If the player focuses on the target for timeToSelect seconds, it "kills" the target, spawns a visual effect (killEffect), increments the score, and moves the target to a new random position.
4. Resets the countdown and stops the hit effect if the player looks away.

---

### **Full Code**
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class KillTarget : MonoBehaviour
{
    public GameObject target;
    public ParticleSystem hitEffect;
    public GameObject killEffect;
    public float timeToSelect = 3.0f;
    public int score;

    private float countDown;

    void Start()
    {
        score = 0;
        countDown = timeToSelect;
    }

    void Update()
    {
        Transform camera = Camera.main.transform;
        Ray ray = new Ray(camera.position, camera.rotation * Vector3.forward);
        RaycastHit hit;

        if (Physics.Raycast(ray, out hit) && (hit.collider.gameObject == target))
        {
            if (countDown > 0f)
            {
                countDown -= Time.deltaTime;
                hitEffect.transform.position = hit.point;
                hitEffect.Play();
            }
            else
            {
                Instantiate(killEffect, target.transform.position, target.transform.rotation);
                score += 1;
                countDown = timeToSelect;
                SetRandomPosition();
            }
        }
        else
        {
            countDown = timeToSelect;
            hitEffect.Stop();
        }
    }

    void SetRandomPosition()
    {
        float x = Random.Range(-5.0f, 5.0f);
        float z = Random.Range(-5.0f, 5.0f);
        target.transform.position = new Vector3(x, 0f, z);
    }
}
```

---