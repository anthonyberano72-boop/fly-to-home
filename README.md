//----------------------------Menu
using UnityEngine;
using UnityEngine.SceneManagement;
public class Menu : MonoBehaviour
{
    public void PlayGame()
    {
        SceneManager.LoadSceneAsync(1);
    }
    public void QuitGame()
    {
        Application.Quit();
    }
}
//----------------------------Audio
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
//--------------------------------------Background
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
//----------------------------------------------Bird
using UnityEngine;
using UnityEngine.InputSystem;

public class Bird : MonoBehaviour
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
//-----------------------------------Woodspawn/clone and random offset
using UnityEngine;

public class woodspawn : MonoBehaviour
{
    public GameObject pipe;
    public float spawnRate = 2f;
    private float timer = 0f;
    public float heightOffset = 10f;

    void Start()
    {
        spawnPipe();
    }

    void Update()
    {
        if (timer < spawnRate)
        {
            timer += Time.deltaTime; 
        }
        else
        {
            spawnPipe();
            timer = 0f;
        }
    }

    void spawnPipe()
    {
        float lowestPoint = transform.position.y - heightOffset;
        float highestPoint = transform.position.y + heightOffset;

        Instantiate(                     
            pipe,
            new Vector3(
                transform.position.x,
                Random.Range(lowestPoint, highestPoint),
                0
            ),
            transform.rotation
        );
    }
}
//---------------------------------------scoring and gameover screen display
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
public class wow_logic : MonoBehaviour
{
    public int playerScore;
    public Text scoreText;
    public GameObject GameOver;

    [ContextMenu("Add Score")]
    public void addScore(int scoreToAdd)
    {
        playerScore = playerScore + scoreToAdd;
        scoreText.text = playerScore.ToString();
    }


    public void restartGame()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }

    public void gameOver()
    {
        GameOver.SetActive(true);
    }

}
//----------------------------- logic for scoring
using UnityEngine;

public class SHEESS : MonoBehaviour
{
    public wow_logic logic;

    void Start()
    {
        logic = GameObject.FindGameObjectWithTag("score").GetComponent<wow_logic>();
    }

    void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.gameObject.layer == 3)
        {
            logic.addScore(1);
        }
    }
}
//--------------------------- woodmovemnt and deletion of cloned wood
using UnityEngine;
using UnityEngine.InputSystem;

public class pipes : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float deadZone = -45f;

    void Update()
    {
        transform.position += Vector3.left * moveSpeed * Time.deltaTime;

        if (transform.position.x < deadZone)
        {
            Debug.Log("Pipe goes bye bye");
            Destroy(gameObject);
        }
    }
}
