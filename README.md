using UnityEngine;

public class Audio : MonoBehaviour
{
    [SerializeField] AudioSource musicScource;

[Header("--------------Sounds-----------")]
    public AudioClip background;

    private void Start()
    {
        musicScource.clip = background;
        musicScource.Play();
    }
}

using UnityEngine;
using System.Collections.Generic;
using System.Collections;

public class background : MonoBehaviour
{
    public float speed;

    [SerializeField] 
    private Renderer bgRenderer;

    void Update()
    {
        bgRenderer.material.mainTextureOffset += new Vector2(speed * Time.deltaTime, 0);
    }
}

using UnityEngine;
using UnityEngine.InputSystem;

public class Birdie : MonoBehaviour
{
    public Rigidbody2D myRigidbody;
    public float flapStrength;
    public wow_logic logic;
    public bool birdIsAlive = true;

    // How far the bird can go off-screen before game over
    public float topLimit = 15f;
    public float bottomLimit = -15f;

    void Start()
    {
        logic = GameObject.FindGameObjectWithTag("score").GetComponent<wow_logic>();
    }

    void Update()
    {
        // Space to flap
        if (Keyboard.current.spaceKey.wasPressedThisFrame && birdIsAlive)
        {
            myRigidbody.linearVelocity = Vector2.up * flapStrength; // FIXED: Use velocity
        }

        // Game over if bird leaves the screen area
        if (transform.position.y > topLimit || transform.position.y < bottomLimit)
        {
            logic.gameOver();
            birdIsAlive = false;
        }
    }

    public void OnCollisionEnter2D(Collision2D collision)
    {
        logic.gameOver();
        birdIsAlive = false;
    }
}
