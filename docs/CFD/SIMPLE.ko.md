# SIMPLE (Semi-Implicit Method for Pressure-Linked Equations)

## SIMPLE 알고리즘이란?

단순해서 혹은 간단해서 (simple) SIMPLE 알고리즘이 아니다.<br>
사실 오히려 그 반대라고 할 수 있다. 본 블로그 주인은 이 알고리즘에 이름을 지은 사람에게 박수를 보내고 싶다.<br>
이름을 정말 잘 지었다고...<br>

CFD는 결국 연속방정식과 운동량 방정식을 푸는 기법이라고 전에 기술한 바 있다.<br>
비압축성 유동에서는 이 방정식에 큰 문제가 발생한다.<br>
바로 연속방정식에 압력에 대한 항이 없다는 것이다.<br>
CFD는 결국 유동장의 압력과 속도를 푸는 것이 주된 목적이다.<br>
그런데 풀어야 할 미지수 중에 하나가 방정식에 포함되어 있지 않다니... <br>
SIMPLE 알고리즘은 위 문제를 해결하기 위해 개발된 알고리즘으로 본격적으로 설명을 해보고자 한다.<br>

## SIMPLE 알고리즘 개요

SIMPLE 알고리즘은 주로 아래 목적들을 달성하기 위한 반복 절차라고 설명하면 된다.<br>

1) 속도장과 압력장을 연결한다.
2) 속도장 보정을 통해 연속 방정식 (질량 보존)을 만족시킨다.

아니 이렇게 간단한 (simple) 목적인데, 왜 CFD 엔지니어들은 그토록 어려워 하는것인가?<br>
왜냐하면 반복 절차가 복잡하기 때문이다.<br>

![SIMPLE 알고리즘](../image/SIMPLE.png)

이제부터 본격적으로 들어가보자.

## SIMPLE 알고리즘 설명
연속방정식과 운동량 방정식은 아래와 같다.

* 연속 방정식<br>
<center>**$\partial\rho\over\partial t$+ $\nabla\cdot(\rho U)$ = 0 <br>**</center>

    + $\rho$ : 유체 밀도<br>
    + $t$ : 시간<br>
    + $U$ : 유체 속도<br>

* 운동량방정식<br>
<center>**$\rho$ ($\partial U\over\partial t$+ $U\cdot\nabla U$) = -$\nabla p+ \mu\nabla^2 u + \rho g$ <br>**</center>

    + $U$ : 유체 속도
    + $p$ : 압력
    + $\mu$ : 점성계수
    + $g$ : 중력가속도 벡터

이 때, 식 기술의 편의를 위해 정상 상태에 비압축성 및 중력은 작용하지 않는다고 가정하자. 그러면 위 두 식은 아래와 같이 된다.

* 연속 방정식<br>
<center>**$\nabla\cdot U$ = 0 <br>**</center>

    + $U$ : 유체 속도<br>

* 운동량방정식<br>
<center>**$U\cdot\nabla U$ = -$\nabla p+ \mu\nabla^2 u$ <br>**</center>

    + $U$ : 유체 속도
    + $p$ : 압력
    + $\mu$ : 점성계수
    + $g$ : 중력가속도 벡터

이 때, 운동량 방정식에 점성항을 왼쪽으로 넘기면 행렬 형태로 아래와 같이 식을 정리할 수 있다.<br>

<center>**$MU = -\nabla p$ <br>**</center>
$M$ : 행렬 계수

$$
\begin{pmatrix}
M_{1,1} & M_{1,2} & M_{1,3} & \dots  & M_{1,n} \\
M_{2,1} & M_{2,2} & M_{2,3} & \dots  & M_{2,n} \\
M_{3,1} & M_{3,2} & M_{3,3} & \dots  & M_{3,n} \\
\vdots  & \vdots  & \vdots  & \ddots & \vdots  \\
M_{n,1} & M_{n,2} & M_{n,3} & \dots  & M_{n,n}^{sym}
\end{pmatrix}
\begin{pmatrix}
U_1 \\
U_2 \\
U_3 \\
\vdots \\
U_n
\end{pmatrix}
=
\begin{pmatrix}
\left( \dfrac{\partial p}{\partial x} \right)_1 \\
\left( \dfrac{\partial p}{\partial x} \right)_2 \\
\left( \dfrac{\partial p}{\partial x} \right)_3 \\
\vdots \\
\left( \dfrac{\partial p}{\partial x} \right)_n
\end{pmatrix}
$$

그 복잡하던 운동량 방정식이 AX=B 형태의 간단한 행렬 형태로 바뀌다니, 정말 놀랍지 아니한가<br>
위 식은 모든 셀에 적용된다. 즉, 셀이 N개면 M 행렬은 NXN 행렬이 된다.<br>
이후, 왼쪽 항을 주대각 성분과 비대각 성분으로 구분하다.<br>
이는 SIMPLE에서 근사화된 보정식을 유도하기 위한 목적이다.<br>
그 이유는 아래에서 자세하게 설명한다.<br>

<center>**$AU - H = -\nabla p$ <br>**</center>

+ $A$ : 주대각 성분 행렬이며, 아래와 같이 표현된다.

$$
\mathcal{A} =
\begin{pmatrix}
A_{1,1} & 0       & 0       & \dots  & 0 \\
0       & A_{2,2} & 0       & \dots  & 0 \\
0       & 0       & A_{3,3} & \dots  & 0 \\
\vdots  & \vdots  & \vdots  & \ddots & \vdots \\
0       & 0       & 0       & \dots  & A_{n,n}
\end{pmatrix}
$$

이 때, A는 쉽게 역행렬을 구할 수 있으며 아래와 같이 표현된다.<br>
AX=B 형태의 행렬 곱에서 X를 구하기 위해, 양변에 A의 역행렬을 취해주는 방법은 가장 흔한 방법이다.<br>
하지만 행렬 M의 역행렬은 쉽게 구할 수 없고, A는 주대각 성분만 남게 되어 쉽게 역행렬을 구할 수 있게 된다.<br>
따라서 보정 단계에서 계산을 획기적으로 줄여준다.<br>

$$
\mathcal{A}^{-1} =
\begin{pmatrix}
\dfrac{1}{A_{1,1}} & 0 & 0 & \dots & 0 \\
0 & \dfrac{1}{A_{2,2}} & 0 & \dots & 0 \\
0 & 0 & \dfrac{1}{A_{3,3}} & \dots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \dots & \dfrac{1}{A_{n,n}}
\end{pmatrix}
$$

또한 H는 off-digonal terms (비대각 성분)으로 비대각 성분과 이전 계산에서 속도로 계산된다.<br>
그리고 아래 식으로 표현한다.<br>

<center>**$H = AU - MU$ <br>**</center>

다시 위로 돌아가서, 운동량 방정식은 아래와 같이 표현할 수 있다.<br>

<center>**$AU - H = -\nabla p$ <br>**</center>

그리고 연속 방정식은 아래와 같이 기술된다.<br>

<center>**$\nabla\cdot U$ = 0 <br>**</center>

이제 압력 방정식을 유도할 수 있다.<br>

연속방정식을 U에 대해 정리하면 아래와 같아진다.<br>

<center>**$AU - H = -\nabla p$ <br>**</center>
<center>**$U = A^{-1}H-A^{-1}\nabla p$ <br>**</center>

이를 연속 방정식에 대입하면 아래와 같아진다.<br>

<center>**$\nabla\cdot U$ = 0 <br>**</center>
<center>**$\nabla \cdot [A^{-1}H-A^{-1}\nabla p] = 0$ <br>**</center>

그러면 p에 대한 Possion equation을 아래와 같이 구할 수 있다.<br>
<center>**$\nabla \cdot (A^{-1}H) = \nabla \cdot (A^{-1}\nabla p)$ <br>**</center>

이제 4개 미지수에 대해 4개 방정식이 만들어졌다. ($U_X, U_Y, U_Z, p$)<br>


<center>**$MU = -\nabla p$ <br>**</center>
<center>**$\nabla \cdot (A^{-1}H) = \nabla \cdot (A^{-1}\nabla p)$ <br>**</center>

그렇다면 이제 어떤 방식으로 방정식을 풀게 될까?<br>

## SIMPLE 알고리즘 풀이

### 1. 운동량 방정식에 대해 속도를 푼다. 이 속도는 연속 방정식을 만족시키지 못한다.<br>
<center>**$MU = -\nabla p$ <br>**</center>

### 2. Possion equation에 대해 압력을 푼다.<br>
<center>**$MU = -\nabla p$ <br>**</center>

### 3. 압력장을 이용해 연속 방정식을 만족시키게 속도장을 보정한다.<br>
<center>**$U = A^{-1}H-A^{-1}\nabla p$ <br>**</center>

### 4. 속도장이 운동량 방정식을 만족시키지 않으므로 이 방식을 반복한다.<br>