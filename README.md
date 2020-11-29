# Bullets-2-Balls

Demo video link - 
Demo link -

## Duties:

Bryce Bond - Mic input, pocket detection and debugging

Ryan Browning - Tutorial & Pool scenery and gun velocity meter

Brighan Schwind - ball and gun physics

## Models:

### Tutorial:
![image1](https://github.com/demandingfawn/bullets2balls/blob/main/Images/unknown%20(1).png)

![image1](https://github.com/demandingfawn/bullets2balls/blob/main/Images/unknown%20(2).png)

### Bar:
![image1](https://github.com/demandingfawn/bullets2balls/blob/main/Images/unknown.png)

![image1](https://github.com/demandingfawn/bullets2balls/blob/main/Images/unknown%20(3).png)

### Pool Table:
![image1](https://github.com/demandingfawn/bullets2balls/blob/main/Images/unknown%20(4).png)

![image1](https://github.com/demandingfawn/bullets2balls/blob/main/Images/unknown%20(5).png)


### City:
![image1](https://github.com/demandingfawn/bullets2balls/blob/main/Images/unknown%20(6).png)

![image1](https://github.com/demandingfawn/bullets2balls/blob/main/Images/unknown%20(7).png)


## Notable Code:
### Gun Behavior:
```
using System.Collections;
using System.Collections.Generic;
using System.Collections.Specialized;
using System.Diagnostics;
using System.Security.Cryptography;
using UnityEngine;

[RequireComponent(typeof(AudioSource))]
public class GunBehavior : MonoBehaviour
{
	
    public GameObject bullet;
    private float speed = 0;
	private int meter_speed = 0;
    private bool held = false;
	public Material meter0;
	public Material meter1;
	public Material meter2;
	public Material meter3;
	public Material meter4;
	public Material meter5;
	public Material meter6;
	public Material meter7;
	public TextMesh textTut;
	public TextMesh textTut1;
	public TextMesh textTut2;
	
	
    void Start()
    {

    }

    void Update()
    {
    //Sets defaults for back of gun
        Material defualt0 = meter0;
		Material defualt1 = meter1;
		Material defualt2 = meter2;
		Material defualt3 = meter3;
		Material defualt4 = meter4;
		Material defualt5 = meter5;
		Material defualt6 = meter6;
		Material defualt7 = meter7;
       /* float db = 20 * Mathf.Log10(Mathf.Abs(MicInput.MicLoudness));
        //Grabs mic input
        float speed = Mathf.Abs(db)*10;
        speed = 1000 - speed;
        if(speed<0)
        {
            speed = 1;
        }*/
        UnityEngine.Debug.Log("Speed is " + speed);
        //Starts Recording audio
        if (Input.GetKeyDown(KeyCode.Space))
        {
            /*GameObject instBullet = Instantiate(bullet, transform.position, Quaternion.identity) as GameObject;
            Rigidbody instBulletRigidbody = instBullet.GetComponent<Rigidbody>();
            instBulletRigidbody.useGravity = false;
            instBulletRigidbody.AddForce(transform.forward * speed);
            Destroy(instBullet, 2.0f);*/
            held = true;
           
        }
        if(held)
        {
            float db = 20 * Mathf.Log10(Mathf.Abs(MicInput.MicLoudness));

            float speedGrab = Mathf.Abs(db) * 10;

            speedGrab = 1000 - speedGrab;
            if (speedGrab > speed)
            {
                speed = speedGrab;
            }
			
			meter_speed = ((int)(speed / 100));
      //Turns on lights based on mic
			switch(meter_speed){
				case 8:
					meter7.SetColor("_EmissionColor", new Color(255,255,255));
					goto case 7;
				case 7:
					meter6.SetColor("_EmissionColor",new Color(255,255,255));
					goto case 6;
				case 6:
					meter5.SetColor("_EmissionColor",new Color(255,255,255));
					goto case 5;
				case 5:
					meter4.SetColor("_EmissionColor",new Color(255,255,255));
					goto case 4;
				case 4:
					meter3.SetColor("_EmissionColor",new Color(255,255,255));
					goto case 3;
				case 3:
					meter2.SetColor("_EmissionColor",new Color(255,255,255));
					goto case 2;
				case 2:
					meter1.SetColor("_EmissionColor",new Color(255,255,255));
					goto case 1;
				case 1:
					meter0.SetColor("_EmissionColor",new Color(255,255,255));
					break;
			}
        }
        if (Input.GetKeyUp(KeyCode.Space))
        {
            held = false;
			if(speed != 0){
				//textTut.text = "";
				//textTut1.text = "Nice!";
				//textTut2.text = "Now hit the ball in a pocket!";
				GameObject instBullet = Instantiate(bullet, transform.position, Quaternion.identity) as GameObject;
				Rigidbody instBulletRigidbody = instBullet.GetComponent<Rigidbody>();
				instBulletRigidbody.useGravity = false;
				instBulletRigidbody.AddForce(transform.forward * speed);
				Destroy(instBullet, 2.0f);
			}
            speed = 0;
			meter_speed = 0;
			meter0.SetColor("_EmissionColor", new Color(0,0,0));
			meter1.SetColor("_EmissionColor", new Color(0,0,0));
			meter2.SetColor("_EmissionColor", new Color(0,0,0));
			meter3.SetColor("_EmissionColor", new Color(0,0,0));
			meter4.SetColor("_EmissionColor", new Color(0,0,0));
			meter5.SetColor("_EmissionColor", new Color(0,0,0));
			meter6.SetColor("_EmissionColor", new Color(0,0,0));
			meter7.SetColor("_EmissionColor", new Color(0,0,0));
        }
    }
}

```

### Mic Input:
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MicInput : MonoBehaviour
{
    #region SingleTon

    public static MicInput Inctance { set; get; }

    #endregion

    public static float MicLoudness;
    public static float MicLoudnessinDecibels;

    private string _device;

    //mic initialization
    public void InitMic()
    {
        if (_device == null)
        {
            _device = Microphone.devices[0];
        }
        _clipRecord = Microphone.Start(_device, true, 999, 44100);
        _isInitialized = true;
    }

    public void StopMicrophone()
    {
        Microphone.End(_device);
        _isInitialized = false;
    }


    AudioClip _clipRecord;
    AudioClip _recordedClip;
    int _sampleWindow = 128;

    //get data from microphone into audioclip
    float MicrophoneLevelMax()
    {
        float levelMax = 0;
        float[] waveData = new float[_sampleWindow];
        int micPosition = Microphone.GetPosition(null) - (_sampleWindow + 1); // null means the first microphone
        if (micPosition < 0) return 0;
        _clipRecord.GetData(waveData, micPosition);
        // Getting a peak on the last 128 samples
        for (int i = 0; i < _sampleWindow; i++)
        {
            float wavePeak = waveData[i] * waveData[i];
            if (levelMax < wavePeak)
            {
                levelMax = wavePeak;
            }
        }
        return levelMax;
    }

    //get data from microphone into audioclip
    float MicrophoneLevelMaxDecibels()
    {

        float db = 20 * Mathf.Log10(Mathf.Abs(MicLoudness));

        return db;
    }

    public float FloatLinearOfClip(AudioClip clip)
    {
        StopMicrophone();

        _recordedClip = clip;

        float levelMax = 0;
        float[] waveData = new float[_recordedClip.samples];

        _recordedClip.GetData(waveData, 0);
        // Getting a peak on the last 128 samples
        for (int i = 0; i < _recordedClip.samples; i++)
        {
            float wavePeak = waveData[i] * waveData[i];
            if (levelMax < wavePeak)
            {
                levelMax = wavePeak;
            }
        }
        return levelMax;
    }

    public float DecibelsOfClip(AudioClip clip)
    {
        StopMicrophone();

        _recordedClip = clip;

        float levelMax = 0;
        float[] waveData = new float[_recordedClip.samples];

        _recordedClip.GetData(waveData, 0);
        // Getting a peak on the last 128 samples
        for (int i = 0; i < _recordedClip.samples; i++)
        {
            float wavePeak = waveData[i] * waveData[i];
            if (levelMax < wavePeak)
            {
                levelMax = wavePeak;
            }
        }

        float db = 20 * Mathf.Log10(Mathf.Abs(levelMax));

        return db;
    }



    void Update()
    {
        // levelMax equals to the highest normalized value power 2, a small number because < 1
        // pass the value to a static var so we can access it from anywhere
        MicLoudness = MicrophoneLevelMax();
        MicLoudnessinDecibels = MicrophoneLevelMaxDecibels();
    }

    bool _isInitialized;
    // start mic when scene starts
    void OnEnable()
    {
        InitMic();
        _isInitialized = true;
        Inctance = this;
    }

    //stop mic when loading a new level or quit application
    void OnDisable()
    {
        StopMicrophone();
    }

    void OnDestroy()
    {
        StopMicrophone();
    }


    // make sure the mic gets started & stopped when application gets focused
    void OnApplicationFocus(bool focus)
    {
        if (focus)
        {
            //Debug.Log("Focus");

            if (!_isInitialized)
            {
                //Debug.Log("Init Mic");
                InitMic();
            }
        }
        if (!focus)
        {
            //Debug.Log("Pause");
            StopMicrophone();
            //Debug.Log("Stop Mic");

        }
    }
}

The mic input does a few things. This records the microphone of a user and caculates the volume of the incoming audio. We then take the recorded volume and convert it into the power for the ball shot in the Gun Behavior script.

```
### Destroy ball:
```
using System.Collections;
using System.Collections.Generic;
using System.Globalization;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class Destroy : MonoBehaviour
{
    public TextMesh textObj_Solids;
	public TextMesh textObj_Stripes;
    private void OnCollisionEnter(Collision collision)
    { 
        if(collision.gameObject.tag =="Ball")
        {
            Destroy(collision.gameObject);
			if(collision.gameObject.name != "Ball/Ammo(Clone)"){
				if(collision.gameObject.name == "8"){
					textObj_Solids.text = "Game";
					textObj_Stripes.text = "Over";
				}
				if (collision.gameObject.name == "1" || collision.gameObject.name == "2" || collision.gameObject.name == "3" || collision.gameObject.name == "4" || collision.gameObject.name == "5" || collision.gameObject.name == "6" || collision.gameObject.name == "7"){
					if (textObj_Solids.text != "Game"){
						textObj_Solids.text = textObj_Solids.text + "|";
					}
				}
				else if (collision.gameObject.name == "9" || collision.gameObject.name == "10" || collision.gameObject.name == "11" || collision.gameObject.name == "12" || collision.gameObject.name == "13" || collision.gameObject.name == "14" || collision.gameObject.name == "15"){
					if (textObj_Stripes.text != "Over"){
						textObj_Stripes.text = textObj_Stripes.text + "|";

					}
				}
			}
        }
    }
}
```



## Links for Models:

Pool Table - https://www.cgtrader.com/free-3d-models/sports/game/billiard-pool-table-4a97b4bb488c81810c3f044c2b44473a

City - https://assetstore.unity.com/packages/3d/environments/urban/simple-city-pack-plain-100348

Cars - https://assetstore.unity.com/packages/3d/vehicles/land/simple-cars-pack-97669

