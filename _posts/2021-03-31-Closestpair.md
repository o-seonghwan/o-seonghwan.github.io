# Closest pair

### 최근접 점의 쌍 찾기



 Closest pair 문제는 모든 점에 대하여 각각의 두 점 사이의 거리를 계산하여 가장 가까운 점의 쌍을 찾는 방법을 이용한다. 간단한 방법으로는 모든 점에 대하여 각각의 두 점 사이의 거리를 계산하는 방법이 있지만, 이 방법은 시간복잡도가 O(n^2)으로 점의 갯수가 많아질수록 걸리는 시간은 기하급수적으로 늘어난다는 단점이 있다. 그래서 보다 효율적인 방법으로 분할 정복 알고리즘을 이용하면 시간복잡도를 O(nlog^2n)으로 단축할 수 있다.

1. 합병 정렬 (Merge Sort)

```java
class Sorter {      //합병 정렬
    private void merge(point[] p, int n, int k, int m) {
        point[] C = new point[m - n + 1];

        int i = n, j = k + 1, t = 0;
        while (i <= k && j <= m) C[t++] = p[i].x < p[j].x ? p[i++] : p[j++];
        while (i <= k) C[t++] = p[i++];
        while (j <= m) C[t++] = p[j++];

        for (int a = n, b = 0; a <= m; a++) p[a] = C[b++];
    }

    public void mergeSort(point p[], int n, int m) {
        if (n >= m) return;
        int k = (n + m) / 2;
        mergeSort(p, n, k);
        mergeSort(p, k + 1, m);
        merge(p, n, k, m);
    }
```
합병 정렬은 입력이 2개의 부분문제로 분할되고, 부분문제의 크기가 1/2로 감소하는 분할 정복 알고리즘이다. n개의 숫자들을 n/2개씩 2개의 부분문제로 분할하고, 각각의 부분문제를 재귀적으로 합병 정렬한다.

위 코드에서는 합병 정렬 알고리즘을 이용하여 배열 p를 x 좌표의 오름차순으로 정렬한다. 배열p의 원소 갯수를 1/2씩 분할하면서 원소의 갯수가 1개가 될때까지 함수를 재귀호출 한다. 분할이 끝나면 원소의 크기를 비교하여 정렬한뒤 합병한다.

2. 최근접 점의 쌍 구하기(Closest Fair)

(그림1)
두 점 사이의 거리를 구하는 공식은 다음과 같다.

(그림2)
평면 S 위에 존재하는 모든 점의 거리 중 가장 가까운 두 점과 두 점 사이의 거리를 구해야 한다. 점이 10개 존재할 때 모든 점 사이의 거리를 비교하면 비교해야 하는 거리의 개수는 45개이다. 따라서 이 수를 줄이기 위해 분할 정복 알고리즘을 사용해야 한다.

(그림3), (그림4)
분할 정복 알고리즘을 수행하려면 평면 S를 SL과 SR로 분할해야 한다. 단, S에 있는 점의 수가 3개 이하이면 더 이상 분할하지 않아야 한다. 점이 2개라면 S를 그대로 리턴하고, 점이 3개라면 3개의 쌍에 대해 최근접 점의 쌍을 리턴해야 한다.

(그림5)
만약 SL과 SR의 최근접 점 사이의 거리 중 작은 값을 d라고 놓는다면, 위 그림과 같이 왼쪽 중간점의 x좌표에 d를 뺀 값과 오른쪽 중간점의 x좌표에 각각 d를 더한 값 사이의 x값을 가진 점들 중 최근접 점의 쌍을 찾는다. (중간점이 하나라면 그 점의 x좌표에 d를 빼고 더한다.)

최근접 점의 쌍을 찾는 분할 정복 알고리즘을 구현한 코드는 아래와 같다.

```java
private void measure_distance(point[] p,int n,int m){       //점과 점사이의 거리측정 및 최솟값 비교
        for(int i=n;i<m;i++){
            for(int j=i+1; j<=m; j++){
                double distance= Math.sqrt(Math.pow(p[j].x-p[i].x,2)+Math.pow(p[j].y-p[i].y,2));
                if(min2 > distance)
                    min2 = distance;
            }
        }
    }
```
점과 점 사이의 거리를 구하기 위해 2차원 평면 상에서 두 점의 거리를 구하는 공식인 (x2-x1)^2+(y2-y1)^2을 이용한다. double형 변수 distance를 선언해 구한 거리의 제곱근을 저장하도록 한다. 각각 점들의 거리 중 최솟값을 구하기 위해 for반복문과 if조건문을 이용해 distance들을 비교한다. distance의 값이 min2 변수의 값 보다 작을 경우 min2에 distance를 저장하도록 하며 최솟값을 구한다.


```java
private void min_distance(point[] p, int n, int k, int m){      //중앙영역 점들 최솟값 구하기
        double x1 = p[k].x - min2;
        double x2 = p[k].x + min2;
        int a = n; int b = m;
        while(p[a].x <= x1 && b>a) a++;
        while(p[b].x >= x2 && b>a) b--;
        measure_distance(p,a,b);
    }
```
앞서 구한 min2를 이용해 중앙영역을 구한다. p에 저장된 값들 중 가운데에 있는 값(k번째에 있는 값)을 기준으로 min2만큼 왼쪽에 있는 값을 x1, 오른쪽에 있는 값을 x2라고 선언한다. while반복문을 이용해 x1과 x2 사이의 값들을 중앙영역으로 지정하고 measure_distance를 호출해 중앙영역에 있는 점들의 거리와 그 중 최솟값을 구한다.

```java
public void divide(point[] p, int n,int m){     //분할 정복
        if(m-n < 3){
            measure_distance(p,n,m);
            return;
        }
        int k = (n+m)/2;
        divide(p,n,k);
        divide(p,k+1,m);
        min_distance(p,n,k,m);
    }
```
앞에서 입력된 배열 p을 합병 정렬을 통해 x의 크기에 따라 배열된 것을 분할을 해야한다. 이때 분할된 영역의 점의 개수가 3개 이상이면 추가적인 거리를 비교해야하기 때문에 다시 분할을 해야하는데, 이를 조건문 if(m-n<3)을 이용해 표현했다. 아래의 코드는 조건을 충족하지 않았을 때 다시 재분할 시키는 것을 보여준다.



분할 정복을 이용하지 않고 구한 최근접 점 사이 거리값과 분할 정복을 이용한 값을 나타내는 전체 코드는 아래와 같다.

```java
import java.util.Scanner;

class point{
    int x;
    int y;
    public point(int x,int y){
        this.x = x;
        this.y = y;
    }
}

class Sorter {      //합병 정렬
    private void merge(point[] p, int n, int k, int m) {
        point[] C = new point[m - n + 1];

        int i = n, j = k + 1, t = 0;
        while (i <= k && j <= m) C[t++] = p[i].x < p[j].x ? p[i++] : p[j++];
        while (i <= k) C[t++] = p[i++];
        while (j <= m) C[t++] = p[j++];

        for (int a = n, b = 0; a <= m; a++) p[a] = C[b++];
    }

    public void mergeSort(point p[], int n, int m) {
        if (n >= m) return;
        int k = (n + m) / 2;
        mergeSort(p, n, k);
        mergeSort(p, k + 1, m);
        merge(p, n, k, m);
    }
}

public class Closest_pair {     //최근접 점의 쌍 분할 정복 알고리즘

    public static double min2 = 100;

    private void measure_distance(point[] p,int n,int m){       //점과 점사이의 거리측정 및 최솟값 비교
        for(int i=n;i<m;i++){
            for(int j=i+1; j<=m; j++){
                double distance= Math.sqrt(Math.pow(p[j].x-p[i].x,2)+Math.pow(p[j].y-p[i].y,2));
                if(min2 > distance)
                    min2 = distance;
            }
        }
    }

    private void min_distance(point[] p, int n, int k, int m){      //중앙영역 점들 최솟값 구하기
        double x1 = p[k].x - min2;
        double x2 = p[k].x + min2;
        int a = n; int b = m;
        while(p[a].x <= x1 && b>a) a++;
        while(p[b].x >= x2 && b>a) b--;
        measure_distance(p,a,b);
    }

    public void divide(point[] p, int n,int m){     //분할 정복
        if(m-n < 3){
            measure_distance(p,n,m);
            return;
        }
        int k = (n+m)/2;
        divide(p,n,k);
        divide(p,k+1,m);
        min_distance(p,n,k,m);
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();

        point[] p = new point[n];

        for (int i = 0; i < p.length; i++)
            p[i] = new point(sc.nextInt(), sc.nextInt());

        System.out.println("입력된 좌표는");
        for (int i = 0; i < p.length; i++)
            System.out.printf("(%d,%d) ", p[i].x, p[i].y);

        Sorter sorter = new Sorter();
        sorter.mergeSort(p,0,p.length-1);

        System.out.println("");
        for (int i = 0; i < p.length; i++)
            System.out.printf("(%d,%d) ", p[i].x, p[i].y);
        System.out.println("");
        double min = Math.sqrt(Math.pow(p[p.length-1].x-p[0].x,2)+Math.pow(p[p.length-1].y-p[0].y,2));

        double CPx1 = 0, CPx2=0, CPy1=0,CPy2=0;

        for(int i = 0; i<p.length; i++) {
            for(int j = i+1; j<p.length; j++){
                double distance = Math.sqrt(Math.pow(p[j].x - p[i].x, 2) + Math.pow(p[j].y - p[i].y, 2));
                if (min > distance)
                {
                    CPx1=p[i].x;
                    CPy1=p[i].y;
                    CPx2=p[j].x;
                    CPy2=p[j].y;
                    min = distance;
                }

            }
        }

        Closest_pair colsestpair = new Closest_pair();
        colsestpair.divide(p,0,p.length-1);


        System.out.println("");
        System.out.printf("최근점의 쌍은 (%f, %f), (%f, %f)입니다. ",CPx1,CPy1,CPx2,CPy2);

        System.out.println("");
        System.out.printf("점들의 거리의 최솟값은 %f",min); //거리 계산

        System.out.println("");
        System.out.printf("분할 정복을 했을 때 최솟값은 %f",min2); //분할 정복

    }
}
```



