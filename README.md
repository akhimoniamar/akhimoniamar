using UnityEngine;

public class BusController : MonoBehaviour
{
    public float speed = 10f;         // Bus speed
    public float turnSpeed = 100f;    // Bus turning speed
    public Transform destination;     // Destination for the bus
    public float timeLimit = 60f;     // Time limit for the game
    private float timeRemaining;

    void Start()
    {
        timeRemaining = timeLimit;  // Set time when game starts
    }

    void Update()
    {
        // Control bus speed and turning
        float moveForward = Input.GetAxis("Vertical") * speed * Time.deltaTime;
        float turn = Input.GetAxis("Horizontal") * turnSpeed * Time.deltaTime;

        transform.Translate(Vector3.forward * moveForward);
        transform.Rotate(0, turn, 0);

        // Move bus towards destination
        if (destination != null)
        {
            Vector3 direction = destination.position - transform.position;
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(direction), 0.1f);
        }

        // Decrease time as game progresses
        timeRemaining -= Time.deltaTime;
        if (timeRemaining <= 0)
        {
            Debug.Log("Time's up! Try again.");
            // Add logic to restart or finish the game
        }
    }
}
using UnityEngine;

public class Traffic : MonoBehaviour
{
    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Bus"))
        {
            // If bus collides with traffic, reduce speed
            other.GetComponent<BusController>().speed /= 2;
            Debug.Log("Traffic Jam!");
        }
    }
}
using UnityEngine;

public class BusRoute : MonoBehaviour
{
    public Transform[] stops;          // Array of bus stops (to represent the route)
    private int currentStopIndex = 0;  // Current stop index
    public float speed = 10f;

    void Update()
    {
        // If bus reaches the stop, move to the next one
        if (Vector3.Distance(transform.position, stops[currentStopIndex].position) < 1f)
        {
            currentStopIndex++;
            if (currentStopIndex >= stops.Length)
            {
                Debug.Log("Route Completed!");
                currentStopIndex = 0;  // Reset to first stop
            }
        }

        // Move bus towards the current stop
        Vector3 direction = stops[currentStopIndex].position - transform.position;
        transform.Translate(direction.normalized * Time.deltaTime * speed, Space.World);
    }
}
using UnityEngine;

public class PassengerService : MonoBehaviour
{
    public float timeLimit = 60f;    // Time limit for the game
    private float timeRemaining;

    void Start()
    {
        timeRemaining = timeLimit;  // Set the time when game starts
    }

    void Update()
    {
        timeRemaining -= Time.deltaTime;
        if (timeRemaining <= 0)
        {
            Debug.Log("Time's up! Try again.");
            // Add logic to restart or finish the game
        }
    }

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("BusStop"))
        {
            Debug.Log("Passenger dropped off!");
            // Add bonus or score logic here after dropping off a passenger
        }
    }
}
using UnityEngine;

public class ProgressionSystem : MonoBehaviour
{
    public int currentLevel = 1;    // Current level
    public int maxLevel = 5;        // Maximum level

    public void CompleteLevel()
    {
        currentLevel++;
        if (currentLevel > maxLevel)
        {
            currentLevel = maxLevel;
            Debug.Log("All levels completed!");
        }

        // Unlock new routes or buses here
    }
}
using UnityEngine;
using UnityEngine.UI;

public class UIManager : MonoBehaviour
{
    public Text timeRemainingText;   // Time remaining text
    public Text levelText;           // Current level text

    public void UpdateTimeRemaining(float time)
    {
        timeRemainingText.text = "Time Left: " + Mathf.Round(time).ToString();
    }

    public void UpdateLevel(int level)
    {
        levelText.text = "Level: " + level.ToString();
    }
}

