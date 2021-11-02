using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class GunSystem : MonoBehaviour {



	//GunStats
	[Range(0,100)]
	public int damage;
	[Range(1,10)]
	public int bulletsPerTap;
	public float timeBetweenShooting, spread, range, reloadTime, timeBetweenShots;
	public int InitialMaxAmmo;//Dnt forget to set initial max ammo equal to max ammo
	public int magazineSize, maxAmmo, currentAmmo;
	public int bulletsShot;
	public string EnemyTag;
	public int bulletsCountForReload;
	public int noOfReloads;
	public float shootForce;
	//bools
	public bool shooting, readyToShoot, reloading;

	//References
	public Camera rcOrigin;
	public Transform attackPoint;
	public RaycastHit rcHitInfo;
	public LayerMask shootable;
	public Transform shellExitPoint;
	public GameObject shell;
	public Vector3 shellExitForce;

	//Graphics
	public CameraShake camShakeScript;
	public float shakeMagnitude, shakeDuration;
	public ParticleSystem muzzleFlash;
	public GameObject bulletImpactStone,bulletImpactMetal;
	public Color noBulletColor;
	//UI Displays
	public Text ammoDisplay;

	//crossHair
	public GameObject Crosshair;
	public Color EnemyCrosshairColor;


	// Use this for initialization
	void Start() /*or Start */
	{
		if (InitialMaxAmmo != maxAmmo) {
			Debug.LogError ("initialMaxAmmo is not set to maxAmmo, make sure it is always set each time the GunSystem script is assigned or editted");
		}
		magazineSize = Mathf.Clamp (magazineSize, 0, 900000000);
		noOfReloads = 0;
		bulletsCountForReload = 0;
		bulletsShot = 0;
		currentAmmo = magazineSize;
		readyToShoot = true;

		Crosshair.transform.localScale = new Vector3 (1f, 1f, 1f);
		Crosshair.SetActive (true);
	}
	// Update is called once per frame
	void Update () 
	{
		MyInput ();

		ammoDisplay.text = currentAmmo/bulletsPerTap + "/" + maxAmmo/bulletsPerTap;
	
		if (Input.GetKeyUp(KeyCode.Mouse0)) 
		{
			shooting = false;
		}

	}





	private void MyInput()
	{
		
			

		if (readyToShoot && !reloading && currentAmmo > 0 && Input.GetKey (KeyCode.Mouse0)) 
		{
			shooting = true;
			shootMultiple ();

		} 
		else if (shooting == false) {
			Crosshair.transform.localScale = new Vector3(1f, 1f, 1f);
			Image _colorOfCrosshairWhenShooting = Crosshair.GetComponentInChildren<Image> ();
			_colorOfCrosshairWhenShooting.color = Color.white;

		}

		if (noOfReloads == InitialMaxAmmo / magazineSize && currentAmmo <= 0) //no bullets
		{
			ammoDisplay.color = noBulletColor;

		}
		else if (currentAmmo <= 0 && !reloading &&  bulletsShot >= magazineSize && noOfReloads != InitialMaxAmmo / magazineSize ) //reload
		{
			StartCoroutine (AutoReload ());
		}

	}



	void Shoot()
	{
		
		readyToShoot = true;
		//spread
		float x = Random.Range (-spread, spread);// bulletSpread on x axis
		float y = Random.Range (-spread, spread);// bulletSpread on y axis
	
		Crosshair.transform.localScale = new Vector3(.6f, .6f, .6f);
	
		//Shell eject
		Rigidbody shellRB = shell.GetComponent<Rigidbody>();
		Instantiate (shell, shellExitPoint);
		shellRB.AddForce (shellExitForce, ForceMode.Impulse);

		//calculate direction with spread
		Vector3 direction = rcOrigin.transform.forward + new Vector3 (x, y, 0f);//adds our spread to our fpsCam's position to offset the raycast

		//RayCast
		/// creates a rayCast and stores it in the rayCastHitInfo 
		if (Physics.Raycast (rcOrigin.transform.position, direction, out rcHitInfo)) {


			Debug.Log (rcHitInfo.collider.name);
			shooting = true;

		
			//showCrosshairs
			Image colorOfCrosshairWhenShooting = Crosshair.GetComponentInChildren<Image> ();

			EnemyStats enemystatsScripts = rcHitInfo.collider.GetComponent<EnemyStats>();
			Rigidbody rb = rcHitInfo.collider.GetComponent<Rigidbody> ();
			if (rb != null) {
				rb.AddForce (-rcHitInfo.normal * shootForce * Time.deltaTime);
			}
		
			//Play muzzleFlash

			if (enemystatsScripts != null ) 
			{
				enemystatsScripts.TakeDamage (damage);

			}	


			if (rcHitInfo.collider.gameObject.layer == 13) 
			{
				colorOfCrosshairWhenShooting.color = EnemyCrosshairColor;
			}



			//AmmoEdit
			bulletsShot ++;
			currentAmmo--;	
			bulletsCountForReload++;

			muzzleFlash.Play ();


		
			// vfx
			if (rcHitInfo.collider.CompareTag ("Concrete"))
			{
				Instantiate (bulletImpactStone, rcHitInfo.point, Quaternion.LookRotation (rcHitInfo.normal));//responsible for the hitEffect
			}

			if (rcHitInfo.collider.CompareTag ("Metal"))
			{
				Instantiate (bulletImpactMetal, rcHitInfo.point, Quaternion.LookRotation (rcHitInfo.normal));//responsible for the hitEffect
			}




			Invoke ("ResetShot", timeBetweenShooting);

		}

		// Graphics
		/// ShakeCamera
		camShakeScript.shake (shakeDuration, shakeMagnitude);
	}
	void ResetShot()
	{
		readyToShoot = true;

	}



	IEnumerator AutoReload()
	{

		readyToShoot = false;
		reloading = true;

		yield return new WaitForSeconds (reloadTime);
		currentAmmo += bulletsShot;
		maxAmmo -= bulletsShot; 
		bulletsShot = 0;
		reloading = false;

		Color colorOfCrossHair = Crosshair.GetComponentInChildren<Image> ().color;
		colorOfCrossHair = Color.white;
		noOfReloads++;
	}
	public IEnumerator StartReload()
	{

		readyToShoot = false;
		reloading = true;

		yield return new WaitForSeconds (reloadTime);
		currentAmmo = magazineSize;
		bulletsShot = 0;
		reloading = false;

		Color colorOfCrossHair = Crosshair.GetComponentInChildren<Image> ().color;
		colorOfCrossHair = Color.white;
		noOfReloads++;
	}


	void shootMultiple()
	{
		if (bulletsPerTap == 1) 
		{
			Shoot ();
		}
		else if (bulletsPerTap == 2) 
		{
			Shoot ();
			Shoot ();

		}

		else if (bulletsPerTap == 3) 
		{
			Shoot ();
			Shoot ();
			Shoot ();

		}else if (bulletsPerTap == 4) 
		{
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();

		}else if (bulletsPerTap == 5) 
		{
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();

		}else if (bulletsPerTap == 6) 
		{
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();

		}else if (bulletsPerTap == 7) 
		{
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();

		}else if (bulletsPerTap == 8) 
		{
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();

		}else if (bulletsPerTap == 9) 
		{
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();

		}else if (bulletsPerTap == 10) 
		{
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();
			Shoot ();

		}




	}
}
