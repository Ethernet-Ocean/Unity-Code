# Unity-Code
This repo contains code for three unique animation states in Unity. 
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class animationStateController : MonoBehaviour
{
    public Transform cameraTransform; // reference to the camera's transform
    public float cameraDistance = 5.0f; // distance between camera and player
    public float cameraHeight = 2.0f; // height of the camera above the player
    public float mouseSensitivity = 100.0f; // sensitivity of mouse movement
    public float maxCameraHeight = 5.0f; // maximum height of the camera above the player
    public float minCameraHeight = 1.0f; // minimum height of the camera above the player
    public float jumpForce = 5.0f; // force applied to the player when jumping
    public LayerMask groundLayer; // layer mask for the ground

    private Vector3 moveDirection = Vector3.zero;
    private Animator _anim;
    private bool _isWalking = false;
    private bool _isRunning = false;
    private bool _isJumping = false;

    private void Start()
    {
        _anim = GetComponent<Animator>();

        // set camera position and rotation
        cameraTransform.position = transform.position - transform.forward * cameraDistance + Vector3.up * cameraHeight;
        cameraTransform.LookAt(transform.position);
    }

    void Update()
    {
        var x = Input.GetAxis("Mouse X") * Time.deltaTime * mouseSensitivity;
        var y = Input.GetAxis("Mouse Y") * Time.deltaTime * mouseSensitivity;

        // rotate player based on mouse movement
        transform.Rotate(0, x, 0);

        // limit camera rotation based on mouse movement
        Vector3 cameraRotation = cameraTransform.rotation.eulerAngles;
        cameraRotation.x -= y;
        cameraTransform.rotation = Quaternion.Euler(cameraRotation);

        // move camera up and down based on mouse movement
        cameraHeight -= y;
        cameraHeight = Mathf.Clamp(cameraHeight, minCameraHeight, maxCameraHeight);

        if (Input.GetKey(KeyCode.LeftShift) && Input.GetKey("w"))
        {
            if (!_isRunning)
            {
                _isRunning = true;
                _anim.Play("Running");
            }
            var z = Input.GetAxis("Vertical") * Time.deltaTime * 6.0f;
            transform.position += transform.forward * z; // Only move when "w" is pressed.
        }
        else if (Input.GetKey("w"))
        {
            if (!_isWalking && !_isRunning)
            {
                _isWalking = true;
                _anim.Play("Walking");
            }
            var z = Input.GetAxis("Vertical") * Time.deltaTime * 3.0f;
            transform.position += transform.forward * z; // Only move when "w" is pressed.
        }
        else
        {
            if (_isWalking || _isRunning)
            {
                _anim.Play("Unarmed Idle 01");
            }
            _isWalking = false;
            _isRunning = false;
        }

        // rotate player and camera left and right based on input
        if (Input.GetKey("a"))
        {
            transform.Rotate(0, -2.0f, 0);
            cameraTransform.RotateAround(transform.position, Vector3.up, -2.0f);
        }
        else if (Input.GetKey("d"))
        {
            transform.Rotate(0, 2.0f, 0);
            cameraTransform.RotateAround(transform.position, Vector3.up, 2.0f);
        }
    }
}
