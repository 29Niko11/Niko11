# using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerAL : MonoBehaviour
{
    Rigidbody2D rb2d;
    public SpriteRenderer PlayerSprite;

    bool isGrounded;
    [SerializeField]
    Transform groundcheck;

    [SerializeField]
    GameObject Ghost;

    [SerializeField]
    private float runSpeed = 15f;
    [SerializeField]
    private float dasSpeed = 10f;
    [SerializeField]
    private float jumpSpeed = 5f;
    private static PlayerAL instance;

    public static int currHealth;
    public int MaxHealth = 5;
    // Start is called before the first frame update
    void Start()
    {
        rb2d = GetComponent<Rigidbody2D>();
        PlayerSprite = GetComponent<SpriteRenderer>();

        currHealth = MaxHealth;
    }
    IEnumerator DashMove()
    {
        runSpeed = dasSpeed;
        yield return new WaitForSeconds(.5f);
        runSpeed = 5;
    }


    private void Update()
    {
        if(currHealth > MaxHealth)
        {
            currHealth = MaxHealth;
        }
        if(currHealth <= 0)
        {
            Die();
        }
    }


    private void FixedUpdate()
    {
        if (Physics2D.Linecast(transform.position, groundcheck.position, 1 << LayerMask.NameToLayer("ground")))
        {
            isGrounded = true;
        }
        else
        {
            isGrounded = false;
        }

        if (Input.GetKey("f"))
        {
            StartCoroutine("DashMove");
            GameObject GhostBaby = Instantiate(Ghost, transform.position, transform.rotation);
        }

        if (Input.GetKey("d") || Input.GetKey("right"))
        {
            rb2d.velocity = new Vector2(runSpeed, rb2d.velocity.y);
        }
        else if (Input.GetKey("a") || Input.GetKey("left"))
        {
            rb2d.velocity = new Vector2(-runSpeed, rb2d.velocity.y);
        }
        else
        {
            rb2d.velocity = new Vector2(0, rb2d.velocity.y);
        }
        if (Input.GetKey("space") && isGrounded)
        {
            rb2d.velocity = new Vector2(rb2d.velocity.x, jumpSpeed);
        }
    }
    public static PlayerAL Instance
    {
        get
        {
            if (instance == null)
            {
                instance = GameObject.FindObjectOfType<PlayerAL>();
            }
            return instance;
        }
    }
    public void Damage (int dmg)
    {

    }

    void Die()
    {
        Application.LoadLevel(Application.loadedLevel);
    }
}
