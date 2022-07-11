```csharp
//============================================
// ATTACH THIS TO WEAPON
//
// ATTACH WEAPON TO PLAYER GAMEOBJECT
//============================================

using UnityEngine;
using System.Collections;

public class WeaponSwayScript : MonoBehaviour {
public float mouseSensitivity = 10f;
public float maxMoveAmount = 0.5f;
public float smoothSpeed = 3f;

private Vector3 startPos;

private float factorX;
private float factorY;

public bool bRotate;
public float smoothRotationSpeed = 2f;
public float rotateAmount = 45f;

//============================================
// FUNCTIONS
//============================================

void Start()
{
    startPos = transform.localPosition;
}

void Update()
{
    // STORE MOUSE MOVEMENT AMOUNTS, SMOOTH WITH LERP
    factorX = Mathf.Lerp(factorX, -Input.GetAxis("Mouse X") * mouseSensitivity, Time.deltaTime * 10f);
    factorY = Mathf.Lerp(factorY, -Input.GetAxis("Mouse Y") * mouseSensitivity, Time.deltaTime * 10f);

    // CLAMP LIMITS
    if (factorX > maxMoveAmount)
        factorX = maxMoveAmount;

    if (factorX < -maxMoveAmount)
        factorX = -maxMoveAmount;

    if (factorY > maxMoveAmount)
        factorY = maxMoveAmount;

    if (factorY < -maxMoveAmount)
        factorY = -maxMoveAmount;

    // SET TARGET POSITION (START POSITION + MOUSE MOVEMENT)
    Vector3 targetPos = new Vector3(startPos.x + factorX, startPos.y + factorY, startPos.z);

    // APPLY POSITION TO WEAPON, SMOOTH WITH LERP
    transform.localPosition = Vector3.Lerp(transform.localPosition, targetPos, smoothSpeed * Time.deltaTime);

    // ROTATION
    if (bRotate)
    {
        float tiltAroundZ = Input.GetAxis("Mouse X") * rotateAmount;
        float tiltAroundX = Input.GetAxis("Mouse Y") * rotateAmount;
        Vector3 target = new Vector3(tiltAroundX, 0f, tiltAroundZ);
        transform.localRotation = Quaternion.Slerp(transform.localRotation, Quaternion.Euler(target), Time.deltaTime * smoothRotationSpeed);
    }
}
}
```