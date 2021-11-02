using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;

public class EnemyAI : MonoBehaviour {

	public GameObject shootPoint, shootPoint2;

	public NavMeshAgent agent;

	public Transform player;

	public LayerMask whatIsGround, whatIsPLayer;

	//Patrolling
	Vector3 walkPoint;
	public float walkPointRange;

	//Attacking
	public float timeBetweenAttacks;
	public float bulletSpeed = 1000f;
	bool alreadyAttacked;

	//state
	public float sightRange, attackRange;
	bool playerInSightRange, PlayerInAttackRange;
	public bool walkPointSet;

	public GameObject projectile;

	public GameObject viserPat, viserSR, viserATK;

	public void Awake()
	{
		player = player.transform;// GameObject.Find ("playerBody")
		agent = GetComponent<NavMeshAgent> ();
	}

	public void Update()
	{
		//check for sight and attack range
		playerInSightRange = Physics.CheckSphere (transform.position, sightRange, whatIsPLayer);
		PlayerInAttackRange = Physics.CheckSphere (transform.position, attackRange, whatIsPLayer);
	

		if (!playerInSightRange && !PlayerInAttackRange)
			patrolling (); 
		if (playerInSightRange && !PlayerInAttackRange)
			chasePlayer (); 
		if (playerInSightRange && PlayerInAttackRange)
			AttackPlayer (); 

	
	}

	private void patrolling()
	{
		viserPat.SetActive (true);
		viserSR.SetActive (false);
		viserATK.SetActive (false);
		if (!walkPointSet)
			SearchWalkPoint ();
		if (walkPointSet) 
			agent.SetDestination (walkPoint);

		Vector3 distanceToWalkPoint = transform.position - walkPoint;

		//walkPoint Reached
		if (distanceToWalkPoint.magnitude < 1f) {
			walkPointSet = false;
		}

		

	}

	public void SearchWalkPoint()
	{
		//calc random point in range
		float randomZ = Random.Range(-walkPointRange, walkPointRange);
		float randomX = Random.Range(-walkPointRange, walkPointRange);

		walkPoint = new Vector3 (transform.position.x + randomX, transform.position.y, transform.position.z + randomZ);
	
		if (Physics.Raycast(walkPoint, -transform.up, 2f, whatIsGround)) 
		{
			walkPointSet = true;
		}

	}
	private void chasePlayer()
	{
		viserPat.SetActive (false);
		viserSR.SetActive (true);
		viserATK.SetActive (false);
		agent.SetDestination (player.position);
	}
	public void AttackPlayer()
	{
		viserPat.SetActive (false);
		viserSR.SetActive (false);
		viserATK.SetActive (true);
		//Make sure enemy dosemt move
		agent.SetDestination(transform.position);

		transform.LookAt (player);


		if (!alreadyAttacked) 
		{
			///attack code here
			Rigidbody rb =Instantiate(projectile,shootPoint.transform.position, Quaternion.identity).GetComponent<Rigidbody>();
			Rigidbody rb2 =Instantiate(projectile,shootPoint2.transform.position, Quaternion.identity).GetComponent<Rigidbody>();

			rb.AddForce (transform.forward * bulletSpeed * Time.deltaTime, ForceMode.Impulse);
			rb.AddForce (transform.up * 1f * Time.deltaTime, ForceMode.Impulse);

			rb2.AddForce (transform.forward * bulletSpeed * Time.deltaTime, ForceMode.Impulse);
			rb2.AddForce (transform.up * 1f * Time.deltaTime, ForceMode.Impulse);



			alreadyAttacked = true;
			Invoke("ResetAttack", timeBetweenAttacks);
		}
	}

	public void ResetAttack()
	{
		alreadyAttacked = false;
	}





	private void OnDrawGizmosSelected()
	{
		Gizmos.color = Color.red;
		Gizmos.DrawWireSphere (transform.position, attackRange);
		Gizmos.color = Color.green;
		Gizmos.DrawWireSphere (transform.position, sightRange);
	}

}
