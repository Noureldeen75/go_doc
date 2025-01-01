# DestroyBall Script

The **DestroyBall** script handles the lifecycle of a GameObject in Unity. It deactivates the object either after a set time (using a timer) or when the object falls below a certain position. This script is commonly used to manage objects like falling items or projectiles in games.

---

## Full Code

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class DestroyBall : MonoBehaviour
{
    public float timer = 15f;  // Time after which the object will be disabled.

    //void Start()
    //{
    //    Destroy(gameObject, timer);
    //}

    void Update()
    {
        if (transform.position.y < -5f)  // Check if the object has fallen below -5 on the Y-axis.
            DisableMe();
            //Destroy(gameObject);
    }

    private void DisableMe()
    {
        gameObject.SetActive(false);  // Deactivate the object instead of destroying it.
    }

    void OnEnable()
    {
        Invoke("DisableMe", timer);  // Schedule deactivation after the specified timer.
    }

    void OnDisable()
    {
        CancelInvoke();  // Cancel any pending Invoke calls when the object is disabled.
    }
}
```

---

## What the Script Does

- Deactivates the object after a specified time (`timer`) using **`Invoke`**.
- Deactivates the object if it falls below a certain position on the Y-axis (`-5`).
- Cancels any scheduled deactivation when the object is disabled.

---

## Step-by-Step Explanation

### 1. Variables

```csharp
public float timer = 15f;  // Time before the object is deactivated.
```

- **`timer`**: Sets how long the object remains active before being automatically deactivated.

---

### 2. Update Method

```csharp
void Update()
{
    if (transform.position.y < -5f)
        DisableMe();
}
```

- **Purpose**: Checks if the object has fallen below a specific Y-axis value (`-5`).
- **Key Features**:
  - If the object falls below the threshold, it is deactivated using the `DisableMe()` method.

---

### 3. DisableMe Method

```csharp
private void DisableMe()
{
    gameObject.SetActive(false);  // Deactivates the GameObject instead of destroying it.
}
```

- **Purpose**: Deactivates the object to make it available for reuse in object pooling systems.

---

### 4. OnEnable Method

```csharp
void OnEnable()
{
    Invoke("DisableMe", timer);  // Schedules the object to be deactivated after `timer` seconds.
}
```

- **Purpose**: Called automatically when the object is activated. Schedules the `DisableMe()` method to run after the `timer` duration.

---

### 5. OnDisable Method

```csharp
void OnDisable()
{
    CancelInvoke();  // Cancels any scheduled `Invoke` calls when the object is deactivated.
}
```

- **Purpose**: Ensures no lingering `Invoke` calls are left when the object is deactivated.

---

## Potential Exam Questions

### True/False

1. **The `DisableMe` method deactivates the GameObject instead of destroying it.**  
   **Answer**: True  

2. **The `OnEnable` method is called whenever the object is reactivated.**  
   **Answer**: True  

3. **The script destroys the GameObject permanently when it falls below -5 on the Y-axis.**  
   **Answer**: False (It deactivates the GameObject instead).  

4. **The `OnDisable` method schedules the object for deactivation.**  
   **Answer**: False (It cancels any scheduled `Invoke` calls).  

---

### Multiple Choice

1. **What does the `DisableMe` method do in this script?**  
   - A) Permanently destroys the object.  
   - B) Deactivates the object for reuse.  
   - C) Moves the object back to its original position.  
   - **Answer**: B  

2. **What triggers the `OnEnable` method?**  
   - A) When the object is first instantiated.  
   - B) Every time the object is activated.  
   - C) When the object is destroyed.  
   - **Answer**: B  

3. **What happens if the object is below -5 on the Y-axis?**  
   - A) It gets destroyed.  
   - B) It is deactivated using `DisableMe()`.  
   - C) Nothing happens.  
   - **Answer**: B  

---

### Fill in the Blanks

1. The **`_________`** method schedules the object to be deactivated after a set duration.  
   **Answer**: OnEnable  

2. The object is deactivated using the **`_________`** method.  
   **Answer**: DisableMe  

3. When the object is disabled, any pending **`_________`** calls are canceled.  
   **Answer**: Invoke  

---

### Short Answer

1. **Why does the script use `SetActive(false)` instead of `Destroy(gameObject)`?**  
   **Answer**: Using `SetActive(false)` makes the object available for reuse in object pooling systems, improving performance by avoiding repeated instantiation and destruction.  

2. **What happens if the `CancelInvoke()` method is not called in `OnDisable()`?**  
   **Answer**: Pending `Invoke` calls might trigger even after the object is deactivated, causing unexpected behavior.  

3. **How can you change the script to destroy the object instead of deactivating it?**  
   **Answer**: Replace `gameObject.SetActive(false)` with `Destroy(gameObject)` in the `DisableMe` method.

---

### Debugging Questions

1. **What happens if the object is reactivated before the timer expires?**  
   **Answer**: The `OnEnable()` method schedules a new `Invoke`, and the old one is canceled by `OnDisable()` when the object is deactivated.  

2. **What happens if the `timer` value is set to 0?**  
   **Answer**: The object will be immediately deactivated upon activation.  

3. **What would happen if the `OnEnable` and `OnDisable` methods are removed?**  
   **Answer**: The object will no longer be deactivated after a fixed time (`timer`), and scheduled `Invoke` calls will persist even when the object is deactivated.

---

### Code Modification Questions

#### 1. How would you log a message when the object is deactivated?

**Answer**: Add a `Debug.Log` statement in the `DisableMe` method:  
```csharp
private void DisableMe()
{
    Debug.Log("Object deactivated: " + gameObject.name);
    gameObject.SetActive(false);
}
```

---

#### 2. How would you reset the object’s position before deactivating it?

**Answer**: Modify the `DisableMe` method to reset the position:  
```csharp
private void DisableMe()
{
    transform.position = Vector3.zero;  // Reset position to the origin.
    gameObject.SetActive(false);
}
```

---

#### 3. How would you make the deactivation threshold (-5 on Y-axis) configurable?

**Answer**: Add a public variable for the threshold:  
```csharp
public float deactivationThreshold = -5f;

void Update()
{
    if (transform.position.y < deactivationThreshold)
        DisableMe();
}
```

