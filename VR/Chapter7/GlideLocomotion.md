# GlideLocomotion Script

This script is used to control character movement in a game. It allows the character to move forward when a button is pressed and to rotate left or right based on player input. Here's the full breakdown.

---

## Full Code

```csharp
using UnityEngine;

public class GlideLocomotion : MonoBehaviour
{
    public float velocity = 0.7f;  // Speed of forward movement.
    public float comfortAngle = 30f;  // Rotation angle when turning.

    private CharacterController character;  // Unity's tool for smooth movement.
    private bool isWalking = false;  // Tracks whether the character is walking.
    private bool hasRotated = true;  // Prevents continuous rotation on input hold.

    void Start()
    {
        character = GetComponent<CharacterController>();  // Assigns the CharacterController.
    }

    void Update()
    {
        // Check movement input.
        if (Input.GetButtonDown("Fire1"))
            isWalking = true;
        else if (Input.GetButtonUp("Fire1"))
            isWalking = false;

        if (isWalking)  // Move forward if walking.
            character.SimpleMove(transform.forward * velocity);

        // Check rotation input.
        float axis = Input.GetAxis("Horizontal");
        if (hasRotated)
        {
            if (axis == 0f)
                hasRotated = false;
        }
        else
        {
            if (axis > 0.5f)  // Rotate right.
            {
                transform.Rotate(0, comfortAngle, 0);
                hasRotated = true;
            }
            if (axis < -0.5f)  // Rotate left.
            {
                transform.Rotate(0, -comfortAngle, 0);
                hasRotated = true;
            }
        }
    }
}
```

---

## What the Script Does

- **Moves the character forward** when the player presses a button.
- **Rotates the character** left or right when the player presses the left or right input (e.g., arrow keys or joystick).
- Uses Unity's `CharacterController` component for smooth movement.

---

## Step-by-Step Explanation

### 1. Variables (Setup and Storage)

```csharp
public float velocity = 0.7f;  // Controls the forward movement speed.
public float comfortAngle = 30f;  // The angle of rotation per input.

private CharacterController character;  // Reference to the CharacterController.
private bool isWalking = false;  // Indicates if the character is moving forward.
private bool hasRotated = true;  // Ensures rotation happens only once per input.
```

- **`velocity`**: Determines how fast the character moves forward.
- **`comfortAngle`**: Defines the rotation angle when turning.
- **`character`**: Reference to Unity's `CharacterController`, which handles smooth character movement.
- **`isWalking`**: Tracks whether the player has initiated forward movement.
- **`hasRotated`**: Prevents continuous rotation while holding input.

---

### 2. Start Method

```csharp
void Start()
{
    character = GetComponent<CharacterController>();
}
```

- **Purpose**: Links the script to the `CharacterController` component on the same GameObject.
- **Key Functionality**:
  - Without the `CharacterController`, the script cannot manage movement.

---

### 3. Update Method

#### A) Checking Input for Movement

```csharp
if (Input.GetButtonDown("Fire1"))
    isWalking = true;
else if (Input.GetButtonUp("Fire1"))
    isWalking = false;

if (isWalking)
    character.SimpleMove(transform.forward * velocity);
```

- **`Input.GetButtonDown("Fire1")`**: Detects if the player presses the "Fire1" button (e.g., left mouse button or Space key).
  - Sets `isWalking` to `true`, initiating forward movement.
- **`Input.GetButtonUp("Fire1")`**: Detects if the player releases the button.
  - Sets `isWalking` to `false`, stopping movement.
- **`character.SimpleMove(transform.forward * velocity)`**: Moves the character forward at the specified speed.

---

#### B) Checking Input for Rotation

```csharp
float axis = Input.GetAxis("Horizontal");
if (hasRotated)
{
    if (axis == 0f)
        hasRotated = false;
}
else
{
    if (axis > 0.5f)  // Rotate right.
    {
        transform.Rotate(0, comfortAngle, 0);
        hasRotated = true;
    }
    if (axis < -0.5f)  // Rotate left.
    {
        transform.Rotate(0, -comfortAngle, 0);
        hasRotated = true;
    }
}
```

- **`Input.GetAxis("Horizontal")`**:
  - Detects horizontal input values (e.g., left/right arrow keys or joystick).
  - Values range between -1 (left) and 1 (right).
- **Rotation Logic**:
  - Rotates the character by `comfortAngle` (positive for right, negative for left).
  - Ensures rotation happens only once per input using the `hasRotated` flag.

---

### 4. Optional Movement Code (Commented-Out)

```csharp
// Vector3 moveDirection = Camera.main.transform.forward;
// moveDirection *= velocity * Time.deltaTime;
// moveDirection.y = 0f;
// transform.position += moveDirection;
// character.Move(moveDirection);
```

- **Purpose**: Alternate movement logic (not used).
- **Key Functionality**:
  - Moves the character based on the camera's direction.
  - Manually updates the position instead of using `SimpleMove`.

---

## Potential Exam Questions

### True/False

1. **The character's speed depends on the `velocity` variable.**  
   **Answer**: True  

2. **The character will keep rotating if the player holds down the turn key.**  
   **Answer**: False (rotation only happens once due to `hasRotated`).  

3. **The `CharacterController` component is required for this script to work.**  
   **Answer**: True  

4. **The `Update()` method is called once during the game.**  
   **Answer**: False (it is called every frame).  

---

### Multiple Choice

1. **What does the `SimpleMove` method do?**  
   - A) Stops the character's movement.  
   - B) Moves the character in the direction of `transform.forward`.  
   - C) Rotates the character 360 degrees.  
   - **Answer**: B  

2. **What is the purpose of the `comfortAngle` variable?**  
   - A) To determine how fast the character moves.  
   - B) To determine how far the character rotates.  
   - C) To reset the character’s position.  
   - **Answer**: B  

---

### Short Answer

1. **Why is the `hasRotated` variable important?**  
   **Answer**: It prevents the character from continuously rotating when the player holds down the turn key.

2. **What would happen if `CharacterController` is not attached to the GameObject?**  
   **Answer**: The script would not work, and errors would occur because `character.SimpleMove` depends on it.

---

### Debugging

1. **What would happen if `velocity` is set to 0?**  
   **Answer**: The character would not move forward, even if `isWalking` is true.

2. **What happens if the `CharacterController` component is removed?**  
   **Answer**: The script would throw a `NullReferenceException` when trying to use `character.SimpleMove`.

---
