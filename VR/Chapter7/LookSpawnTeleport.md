# LookSpawnTeleport Script

```csharp
using UnityEngine;

public class LookSpawnTeleport : MonoBehaviour
{
    private Color saveColor; // Stores the original color of the object.
    private GameObject currentTarget; // Tracks the currently highlighted object.

    void Update()
    {
        Transform camera = Camera.main.transform; // Get the camera's transform.
        Ray ray = new Ray(camera.position, camera.rotation * Vector3.forward); // Create a ray from the camera.
        RaycastHit hit; // Stores raycast collision information.
        GameObject hitTarget; // The object hit by the ray.

        // Perform a raycast to detect objects in the "TeleportSpawn" layer.
        if (Physics.Raycast(ray, out hit, 10f, LayerMask.GetMask("TeleportSpawn")))
        {
            hitTarget = hit.collider.gameObject; // Get the hit object.
            if (hitTarget != currentTarget) // Check if the hit object is a new target.
            {
                Unhighlight(); // Remove highlight from the old target.
                Highlight(hitTarget); // Highlight the new target.
            }

            // Teleport the player to the object's position when the button is pressed.
            if (Input.GetButtonDown("Fire1"))
            {
                transform.position = hitTarget.transform.position;
                transform.rotation = hitTarget.transform.rotation;
            }
        }
        else if (currentTarget != null) // If no valid target is hit:
        {
            Unhighlight(); // Remove highlight from the old target.
        }
    }

    private void Highlight(GameObject target)
    {
        Material material = target.GetComponent<Renderer>().material; // Access the object's material.
        saveColor = material.color; // Save the original color.
        Color hiColor = material.color;
        hiColor.a = 0.8f; // Make the color slightly more opaque.
        material.color = hiColor; // Apply the highlight color.
        currentTarget = target; // Update the current target.
    }

    private void Unhighlight()
    {
        if (currentTarget != null) // If there's a highlighted object:
        {
            Material material = currentTarget.GetComponent<Renderer>().material; // Access its material.
            material.color = saveColor; // Restore the original color.
            currentTarget = null; // Clear the current target.
        }
    }
}
```

---

## What the Script Does

- **Highlights objects** when the player looks at them.
- **Teleports the player** to the target object's position and orientation when a button is pressed.
- Uses raycasting to detect objects in the "TeleportSpawn" layer.

---

## Step-by-Step Explanation

### 1. Variables (Setup and Storage)

```csharp
private Color saveColor; // Stores the original color of the object being highlighted.
private GameObject currentTarget; // Tracks the currently highlighted object.
```

- **`saveColor`**: Used to store the original color of an object so it can be restored after unhighlighting.
- **`currentTarget`**: Keeps track of the GameObject that is currently highlighted by the player.

---

### 2. Update Method

The **`Update()`** method runs every frame and handles raycasting, highlighting, and teleportation logic.

#### A) Raycasting to Detect Targets

```csharp
Transform camera = Camera.main.transform; 
Ray ray = new Ray(camera.position, camera.rotation * Vector3.forward);
if (Physics.Raycast(ray, out hit, 10f, LayerMask.GetMask("TeleportSpawn")))
{
    hitTarget = hit.collider.gameObject;
    // Check and update the target if necessary.
}
```

- **`Camera.main.transform`**: Gets the player's camera to cast a ray from its position.
- **`Physics.Raycast`**:
  - Casts a ray forward from the camera.
  - Detects objects up to 10 units away on the "TeleportSpawn" layer.
- **`hit.collider.gameObject`**: Retrieves the GameObject hit by the ray.

#### B) Highlighting Logic

```csharp
if (hitTarget != currentTarget)
{
    Unhighlight(); // Unhighlight the previously targeted object.
    Highlight(hitTarget); // Highlight the newly targeted object.
}
```

- **`Unhighlight()`**: Restores the original color of the previously highlighted object.
- **`Highlight()`**: Changes the color of the new target to indicate it is being looked at.

#### C) Teleportation

```csharp
if (Input.GetButtonDown("Fire1"))
{
    transform.position = hitTarget.transform.position;
    transform.rotation = hitTarget.transform.rotation;
}
```

- **`Input.GetButtonDown("Fire1")`**: Checks if the "Fire1" button is pressed.
- **`transform.position` and `transform.rotation`**: Updates the player's position and orientation to match the target object's.

#### D) Unhighlighting When No Target is Hit

```csharp
else if (currentTarget != null)
{
    Unhighlight();
}
```

- If no valid object is hit, the previously highlighted object is unhighlighted.

---

### 3. Highlight Method

```csharp
private void Highlight(GameObject target)
{
    Material material = target.GetComponent<Renderer>().material;
    saveColor = material.color; // Save the original color.
    Color hiColor = material.color;
    hiColor.a = 0.8f; // Make the object more opaque.
    material.color = hiColor; // Apply the highlight color.
    currentTarget = target; // Update the current target.
}
```

- Retrieves the object's material using its `Renderer` component.
- Changes the object's transparency to make it stand out.

---

### 4. Unhighlight Method

```csharp
private void Unhighlight()
{
    if (currentTarget != null)
    {
        Material material = currentTarget.GetComponent<Renderer>().material;
        material.color = saveColor; // Restore the original color.
        currentTarget = null; // Clear the current target.
    }
}
```

- Resets the material color of the last highlighted object to its original state.

---

## Potential Exam Questions

### 1. What is the purpose of the `Highlight` method in the script?

**Answer**:  
The `Highlight` method changes the color of an object to indicate it is being targeted by the player.

---

### 2. How does the script detect which object the player is looking at?

**Answer**:  
The script uses a raycast from the player's camera to detect objects in the "TeleportSpawn" layer within a range of 10 units.

---

### 3. What happens when the player presses the "Fire1" button?

**Answer**:  
The player's position and rotation are updated to match the position and rotation of the currently targeted object.

---

### 4. Why is the `saveColor` variable necessary?

**Answer**:  
The `saveColor` variable stores the original color of the object being highlighted so that it can be restored when the object is unhighlighted.

---

## True/False

1. The script highlights all objects hit by the raycast.  
   **Answer**: False (It only highlights the closest object in the "TeleportSpawn" layer).

2. The `Unhighlight` method restores the original color of the last targeted object.  
   **Answer**: True  

3. The script teleports the player to the object's position but does not change the player's rotation.  
   **Answer**: False (It updates both position and rotation).

---

## Multiple Choice

1. What is the role of `LayerMask.GetMask("TeleportSpawn")` in the script?  
   - A) It highlights objects in the scene.  
   - B) It limits the raycast to objects in the specified layer.  
   - C) It determines the transparency of the target.  
   - **Answer**: B  

2. What happens when no valid object is hit by the raycast?  
   - A) The player teleports to the last targeted object.  
   - B) The script does nothing.  
   - C) The last highlighted object is unhighlighted.  
   - **Answer**: C  

---
