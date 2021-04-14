---
title: "Job Scheduling"
excerpt: "작업 스케줄링 알고리즘"

categories:
  - 과제
tags:
  - 알고리즘
  - java
---



# Greedy#2

# 컴퓨터 알고리즘 3번째 과제

## 팀원: 오성환, 전수연, 최태성

## 1. Job Scheduling이란?

**Job Scheduling Algorithm**(작업 스케줄링 알고리즘)은 **Greedy Algorithm**에 속하는 알고리즘이다.

기계에서 수행하는 n개의 작업(t1, t2, ... , tn)이 있고, 각 작업에 시작 시간과 종료 시간이 존재한다고 할 때 최소한의 기계를 배치하면서 수행 시간이 중복되지 않도록 모든 작업을 수행하도록 하는 문제를 말한다.

작업 스케줄링은 시작시간, 종료시간, 그리고 작업의 길이를 바탕으로 알고리즘을 구현할 수 있다.

- 빠른 시작시간 작업 우선 (Earliest start time first) 배정
- 빠른 종료시간 작업 우선 (Earliest finish time first) 배정
- 짧은 작업 우선 (Shortest job first) 배정
- 긴 작업 우선 (Longest job first) 배정

이 네 가지 우선 원칙 중 1번째 원칙을 제외하고는 그리디 알고리즘을 사용하는 상황에서 최적해를 보장하지 못한다.

## 2. 설계과정, 수도 코드

**Job Scheduling Algorithm**(작업 스케줄링 알고리즘)을 간단히 설명하면 아래와 같다.

- 입력: n개의 작업 t1, t2, ⋯, tn
- 출력: 각 기계에 배정된 작업 순서

1. 시작시간의 오름차순으로 정렬한 작업 리스트: L
2. while ( L ≠ ∅ ) {
3.  L에서 가장 이른 시작시간 작업 ti를 가져온다.
4.  if (ti를 수행할 기계가 있으면)
5.  ti를 수행할 수 있는 기계에 배정한다.
6.  else
7.  새로운 기계에 ti를 배정한다.
8.  ti를 L에서 제거한다. }
9. return 각 기계에 배정된 작업 순서

## 3. 알고리즘의 성능 평가

JobScheduling 시간복잡도

Line1에서 n개의 작업을 시작시간의 오름차순으로 작업리스트 L에 정렬하는데 O(nlogn)의 시간이 걸린다. Line2에서부터 while-루프작업을 L에서 가져다가 수행 가능한 기계를 찾아서 배정하므로 O(m) 시간이 걸린다. (여기서 m은 사용된 기계의 수이다) while-루프가 수행된 총 횟수는 작업의 개수인 n번이므로, line 2~9까지는 O(m)xn=O(mn) 시간이 걸린다.

따라서 JobScheduling 알고리즘의 시간복잡도는 O(nlogn)+O(mn) 이다.

각 작업의 시작시간과 종료시간에 따라 최소한의 기계를 배치해야 최적해를 구할 수 있다. 배정되는 기계의 수 m이 작업의 수 n에 가까워질수록 수행 속도가 느려져 최적해를 구할 수 없게 된다.

## 4. 자바 코드

```
import java.util.*;

class Task implements Comparable<Task>{     //Comparable을 상속받는 Task 클래스 선언
    int start;
    int end;
    Task(int start, int end){               //작업의 시작시간과 종료시간을 입력
        this.start = start;
        this.end = end;
    }
    public int compareTo(Task o){ return this.start - o.start; }
}

public class Main {

    static int TaskSize;        //전역변수 TaskSize, MachineSize 선언
    static int MachineSize = 0;

    public void delete_index(Task[] task){          //배열의 인덱스 제거 메소드
        for(int i = 1; i<TaskSize; i++){
            task[i].start = task[i+1].start;
            task[i].end = task[i+1].end;
        }
        TaskSize--;
    }
    //for task[i]를 task[i+1]로 초기화 시킨다.
    //TaskSize--

    public int[][] JobScheduling(Task[] task){
        int[][] machine = new int[task.length][10];
        int t = 1;
        while(TaskSize>0) {
            int i;
            for (i = 0; i <= MachineSize; i++) {
                if (machine[i][task[1].start] == 0) {
                    for (int j = task[1].start; j < task[1].end; j++)
                        machine[i][j] = t;
                    break;
                }
            }
            if (machine[i][task[1].start] == 0) {
                MachineSize++;
                for (int j = task[1].start; j < task[1].end; j++)
                    machine[MachineSize][j] = t;
            }
            delete_index(task);
            t++;
        }
        return machine;
    }
    //작업 중에서 가장 이른 시작시간 task[1]을 가져온다.
    //if task[1]을 수행할 기계가 있으면 그 기계에 task[1]을 배정한다.
    //else 새로운 기계에 task[1]을 배정한다.
    //작업에서 task[1]을 제거한다.
    //모든작업들이 기계에 배정될때까지 반복한다.

    public static void main(String[] args) {
        TaskSize = 7;
        Task[] task= new Task[TaskSize+1];      //Task 배열 생성.
        task[0] = new Task(0,0);                //배열의 0번인덱스는 0으로 초기화
        task[1] = new Task(7,8);                //작업의 시작시간 종료시간 입력.
        task[2] = new Task(3,7);
        task[3] = new Task(1,5);
        task[4] = new Task(5,9);
        task[5] = new Task(0,2);
        task[6] = new Task(6,8);
        task[7] = new Task(1,6);

        for(int i=1; i< task.length; i++){
            System.out.printf("%d: %d %d",i, task[i].start, task[i].end);
            System.out.println();
        }
        System.out.println();
            Arrays.sort(task);                   //task[] 정렬

        for(int i=1; i< task.length; i++){
            System.out.printf("%d: %d %d",i, task[i].start, task[i].end);
            System.out.println();
        }
        System.out.println();
        Main test = new Main();
        int[][] test2;
        test2 = test.JobScheduling(task);

        for(int i=0; i <= MachineSize; i++) {            //결과 출력
            for (int j = 0; j < 10; j++) {
                System.out.printf("%d ", test2[i][j]);
            }
            System.out.println();
        }
    }
}
```

## 5. 코드 결과

[![그림01](https://user-images.githubusercontent.com/80510972/114697627-1c74d100-9d59-11eb-803e-e8fb298e1efb.png)](https://user-images.githubusercontent.com/80510972/114697627-1c74d100-9d59-11eb-803e-e8fb298e1efb.png)

위 그림과 같이 7개의 작업 t1~t7을 최소한의 기계로 배정할 수 있도록 설정해 Job Scheduling 알고리즘을 돌려보았다.

[![제목 없음](https://user-images.githubusercontent.com/80510972/114698236-d66c3d00-9d59-11eb-8d9e-454bc13a9bb3.png)](https://user-images.githubusercontent.com/80510972/114698236-d66c3d00-9d59-11eb-8d9e-454bc13a9bb3.png)

그림과 같이 t1~t7의 시간이 가장 최소한의 기계에 배치된 것을 확인할 수 있다.