# LR-24[LR-24.zip](https://github.com/Maxsim2418/LR-24/files/10146079/LR-24.zip)

Борнеман М.А

ЭВТ-70

Игровой движок:Unity

Лабораторная работа №24

Тема: Разработка игры Circle

Цель: приобрести навыки в разработке игрового проекта Circle

Ход работы:

1.	Выполнение работы

Создание игрового проекта Circle

1.	Настроил игровую сцену: добавил спрайты, сделал модель игрока, построил блоки, префабы, счёт прыжков игрока и алмазов, анимацию при получении алмазов.

![image](https://user-images.githubusercontent.com/119674602/205433877-c9ce233f-ffc1-419e-b0c9-ad8fa5eac275.png)

Рисунок 24.1 Постройка сцены.

2.	Листинг Player.cs

Скрипт для управления игроком и его постоянного перемещения, а так же добавлена сила прыжков.

using UnityEngine;

public class Player : MonoBehaviour
{
    public Vector2 jumpForce;
    Vector2 currentVelocity;
    Rigidbody2D rgbd;
    GameManager gameManager;
    ScoreUI scoreUi;
    void Start()
    {
        rgbd = GetComponent<Rigidbody2D>();
        rgbd.gravityScale = 0;
        gameManager = FindObjectOfType<GameManager>();
        scoreUi = FindObjectOfType<ScoreUI>();
    }

    void Update()
    {
        if(gameManager.gameOver)
        {
            rgbd.bodyType = RigidbodyType2D.Static;
            return;
        }
        if(Input.GetMouseButtonDown(0))
        {
            if (rgbd.gravityScale != 1)
            { 
                rgbd.gravityScale = 1; 
            }
            rgbd.AddForce(jumpForce);
            SpeedController();
            scoreUi.IncrementScore(1);
        }
    }
    void SpeedController()
    {
        currentVelocity = rgbd.velocity;
        currentVelocity.x = Mathf.Clamp(currentVelocity.x, 2, 2);
        currentVelocity.y = Mathf.Clamp(currentVelocity.y, 0, 2);
        rgbd.velocity = currentVelocity;
    }
}

3.	Листинг Block.cs 

Был написан скрипт блоков, из которых состоят препятствия, при прикосновении к ним, управление игрока полностью отключается (поражение)

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Block : MonoBehaviour
{
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.tag == "Player")
        {
            Debug.Log("hit by player");
            FindObjectOfType<GameManager>().gameOver = true;
        }
    }
}

4.	Листинг GameManager.cs

Был написан скрипт для добавления функции поражения.

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameManager : MonoBehaviour
{
    public bool gameOver;
}

5.	Листинг CameraController.cs 

Был написан скрипт, который фиксирует камеру на игроке.

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraController : MonoBehaviour
{
    public Transform playerTransform;
    void Update()
    {
        transform.position = new Vector3(playerTransform.position.x, transform.position.y, -10);
    }
}

6.	Листинг Diamond.cs

Был написан скрипт, который определяет объект как собираемый.

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Diamond : MonoBehaviour
{
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.tag == "Player")
        {
            FindObjectOfType<ScoreUI>().IncrementDiamond(1);
            FindObjectOfType<ScorePointCanvas>().DiamondHit(collision.transform.position);
            Destroy(gameObject);
        }
    }
}


7.	Листинг DiamondInstantiator.cs 

Был написан скрипт, который отвечает за спавн собираемых объектов.

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class DiamondInstantiator : MonoBehaviour
{
    Transform[] childTransform;
    public GameObject DiamondPrefab;
    void Awake()
    {
        childTransform = new Transform[transform.childCount];
        for (int i = 0; i < childTransform.Length; i++)
        {
            childTransform[i] = transform.GetChild(i);
        }
        InstantiateDiamond();
    }
    void InstantiateDiamond()
    {
        for (int i = 0; i < childTransform.Length; i++)
        {
            if(Random.value > 0.4f)
            {
                Instantiate(DiamondPrefab, childTransform[i].position, Quaternion.identity);
            }
        }
    }
}



8.	Листинг ScoreUI.cs

Был написан скрипт функционирования интерфейса игрока: счёт прыжков и собираемых объектов.

using UnityEngine;

public class ScoreUI : MonoBehaviour
{
    int Score,Diamond;
    public TMPro.TextMeshProUGUI ScoreText;
    public TMPro.TextMeshProUGUI DiamondText;

    public void IncrementScore(int value)
    {
        Score += value;
        UpdateDisplay();
    }

    public void IncrementDiamond(int value)
    {
        Diamond += value;
        UpdateDisplay();
    }

    void UpdateDisplay()
    {
        ScoreText.text = Score.ToString();
        DiamondText.text = Diamond.ToString();
    }
}

9.	Листинг ScorePointCanvas.cs

Был написан скрипт для запуска анимации при подборе объектов.

using UnityEngine;

public class ScorePointCanvas : MonoBehaviour
{
    Animator animator;
    void Start()
    {
        animator = GetComponent<Animator>();
    }
    public void DiamondHit(Vector2 position)
    {
        transform.position = position;
        animator.SetTrigger("Play");
    }
}

2.	Вывод.

В ходе проделанной работы был разработан игровой проект Circle.
