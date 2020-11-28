## Suffix Array
어떤 문자열 $$S[0...n-1]$$의 모든 접미사를 사전 순으로 정렬해 둔 것. 
이 때 공간 및 계산 효율을 위해 접미사 $$S[i...]$$에 대응하는 정수 $$i$$로 표현한다.

## Manber-Myers Algorithm
맨버-마이어스의 알고리즘은 접미사들의 목록을 $$1, 2, ..., 2^k$$ 글자를 기준으로 여러 번 정렬하여 접미사 배열을 구하는 알고리즘이다. 
핵심 아이디어는 이전 정렬로 부터 두 접미사의 길이 $$H$$인 접두사의 대소 관계를 알고 있으면, 이를 통해 같은 접미사들의 길이 $$2H$$인 접두사 간의 대소 관계를 $$O(1)$$에 알 수 있다는 것이다. 
이를 이용하여 접미사들을 $$O(\log N)$$번 정렬하면 답을 얻게 된다. 
이 때 시간 복잡도는 $$O(N\log^2 N)$$ 이다. 
비교 기반의 정렬이 아닌 기수 정렬을 사용하면 시간 복잡도를 $$O(N\log N)$$까지 개선할 수 있다.  
이 글에서는 기수 정렬을 사용한 맨버-마이어스 알고리즘을 작성하고 그 정당성과 시간 복잡도를 증명한다.  

## Notation
이 글에서 사용한 기호들의 의미는 다음과 같다.

| Symbol | Definition |
|---|:---|
|$$\mathbb{S}$$ | $$\mathbb{S}=[0, n-1]$$ 접미사들의 집합 |
|$$\mathbb{F}$$ | $$\mathbb{S} \mapsto \mathbb{S}$$ 이고 일대일 대응인 함수들의 집합  |
|$$C(i)$$ | 접미사 배열의 $$i$$ 번째 값 |
|$$C^{-1}(i)$$ | $$C(x)=i$$ 일 때의 $$x$$ |
|$$C_H(i)$$ | 접미사들을 앞에서부터 $$H$$ 글자를 기준으로 사전 순으로, 사전상 순서 같은 경우에는 긴 것 부터 나열했을 때, $$i$$번째 값 |
|$$C_H^{-1}(i)$$ | $$C_H(x)=i$$일 때의 $$x$$ |
|$$S_H(i)$$ | $$S[i...\min(n-1,i+H-1)]$$ |
|$$G_H(i)$$ | $$\{S_H(0), S_H(1), ..., S_H(n-1)\}$$ 에서 $$S_H(i)$$의 사전상 순서 (0-based) |
|$$\max (G_H)$$ | $$\max (\{S_H(0), S_H(1), ..., S_H(n-1)\})$$ |
|$$L(i)$$ | $$n-i$$ = 접미사 $$i$$의 길이 |
|$$\overset{\mathrm{H}}{=}$$| $$i\overset{\mathrm{H}}{=}j \iff S_H(i)=S_H(j)$$ (lexicographically equal)|  
|$$\overset{\mathrm{H}}{\neq}$$| $$i\overset{\mathrm{H}}{\neq}j \iff S_H(i)<S_H(j) \lor S_H(i)>S_H(j)$$ (lexicographically not equal)|  
|$$\overset{\mathrm{H}}{<}$$| $$i\overset{\mathrm{H}}{<}j \iff S_H(i)<S_H(j)$$ (lexicographically less) |
|$$\overset{\mathrm{H}}{>}$$| $$i\overset{\mathrm{H}}{>}j \iff S_H(i)>S_H(j)$$ (lexicographically greater) |   

$$\mathbb{F}$$에 속하는 함수들은 접미사에서 접미사로 대응되는 함수로 생각할 수 있다.  
예를 들어
$$
f(i)=\begin{cases}
C(i)+2, & \mbox{if } n-i\geq 2 \\
C(i), & \mbox{if } n-i<2
\end{cases}
$$   

로 정의된 $$f(i)$$가 있다고 하자.  
$$f(i)$$는 입력으로 받은 접미사 $$i$$의 길이가 2 이상이라면 앞의 2 글자를 제거한 접미사로 정의되고, 
2 미만의 길이를 가진 접미사에 대해서는 $$i$$와 같은 접미사로 정의된다.
한편 $$G_H\notin \mathbb{F}$$이다. 정의에 의해 $$G_H$$함수는 $$H$$글자 기준으로 두 접미사의 사전상 순서의 대소관계를 나타낸다. 
같은 $$G_H$$값을 가지는 접미사들 끼리는 같은 그룹에 속한다고 하자. 같은 그룹에 속하는 접미사들의 개수를 해당 그룹의 크기라고 하자. 
또한 $$n$$번째 글자에서 시작하는 접미사는 존재하지 않지만 구현상의 편의를 위해 $$G_H(n)=-1$$으로 정의하자 $$G_H(n)$$은 항상 크기 1인 그룹을 이룬다.
$$G_H\in [0, n]\mapsto \{-1,0\}\cup \mathbb{N}$$이다.
이 외에도 다음의 몇 가지 성질이 성립한다.

> **Fact1.**   
$$C=C_N=C_{2^{\lceil \log_{2} N \rceil}}$$

**Proof.**  ∎    
$$C_H$$를 구하는 과정을 최대 $$\lceil\log_{2} N\rceil$$번 반복하면 접미사 배열을 얻을 수 있다.

> **Fact2.**   
1. $$x\overset{\mathrm{H}}{=}y \iff G_H(x)=G_H(y)$$   
2. $$x\overset{\mathrm{H}}{<}y \iff G_H(x)<G_H(y)$$   
3. $$x\overset{\mathrm{H}}{>}y \iff G_H(x)>G_H(y)$$   

**Proof.**  ∎

## Stable Radix Sort
앞서 언급한 대로 비교 기반의 정렬 알고리즘을 사용하면, 접미사들을 $$\lceil\log_{2} N\rceil$$번 정렬해야 하므로 $$O(N\log^2 N)$$ 시간 복잡도를 가진다. $$O(N\log N)$$ 시간 복잡도를 가지는 맨버-마이어스 알고리즘의 핵심 아이디어는 안정한 기수 정렬을 통해 $$C_H$$로부터 $$C_{2H}$$를 $$O(N)$$에 계산할 수 있다는 것이다.
여기서 부터는 설명의 편의상 배열 $$f[...i...]$$과 함수 $$f(i)$$인 사이의 구분을 하지 않고 의미를 혼용하여 사용하겠다. 

> **Algorithm1. (Radix sort)**   
Input: $$P_H\in \mathbb{F}$$, $$G_H$$, $$\max (G_H)$$   
Output: $$C_{2H}^*\in \mathbb{F}$$   
>
> **for** $$i$$ **in** $$[0, \max (G_H)+1]$$ **do**   
$$\quad$$$$I[i] \gets 0$$   
   
> **for** $$i$$ **in** $$[0,n-1]$$ **do**    
$$\quad$$Increase $$I[G_H[P_H[i]]]$$ by 1   
   
> **for** $$i$$ **in** $$[1, \max (G_H)+1]$$ **do**   
$$\quad$$$$I[i] \gets I[i-1]+1$$   
   
> **for** $$i$$ **in** $$[0, n-1]$$ **do**   
$$\quad$$$$j \gets n-1-i$$   
$$\quad$$Decrease $$I[G_H[P_H[j]]]$$ by 1   
$$\quad$$$$C_{2H}^*[I[G_H[P_H[j]]] \gets P_H[j]$$   
   
이 알고리즘의 정당성 및 시간 복잡도가 $$O(N)$$이라는 사실의 증명은 생략한다. 
정렬이 수행되고 나면 $$C_{2H}^*[i]$$는 $$G_H[C_{2H}^*[i]]$$가 오름 차순이 되도록 정렬된다.  
또한 안정한 정렬이므로 $$i<j$$에 대해 $$G_H[P_H[i]]=G_H[P_H[j]]$$ 라면, $$C_{2H}^{*-1}[P_H[i]]<C_{2H}^{*-1}[P_H[j]]$$가 성립한다.
즉, 같은 $$G_H$$값을 가지게 하는 $$P_H[i]$$, $$P_H[j]$$가 있으면, $$i, j$$의 대소 관계가 $$C_{2H}^*[x]=P_H[i]$$인 $$x$$와 $$C_{2H}^*[y]=P_H[j]$$인 $$y$$사이의 대소 관계와 일치한다. 
이 때 $$P_H$$는 같은 $$G_H$$값을 가지는 서로 다른 두 접미사에 대해 우선순위를 나타낸다. 
따라서 $$P_H$$를 priority array이라고 하자. 
$$G_H$$가 다른 두 접미사에 대해서는 $$P_H$$가 영향을 주지 않는다. 
하지만 $$P_H\in \mathbb{F}$$ 일 때만 제대로 정렬이 된다는 것에 주의하자. 
이 때 $$P_H$$는 접미사에서 접미사를 대응시키는 의미가 아니라 $$n$$개의 접미사들의 우선순위 나열이라고 생각하면 이해가 쉽다. 
한편 $$P_H$$가 특정한 조건을 만족하면, $$C_{2H}^*=C_{2H}$$가 된다.

## Calculation of 2H-Sized Suffix Array

> **Lemma1.** 문자열 $$S[0...n-1]$$에서 $$L(i)<H $$이면 접미사 $$i$$는 크기 1인 그룹을 이룬다.   
    
**Proof.**   
결론을 부정하여 $$j\neq i$$가 $$i$$와 같은 그룹에 속한다고 가정하자.
$$G_H(i)=G_H(j) \implies L(i)\geq H \land L(j)\geq H$$ 이어서 $$L(i)<H$$라는 조건에 모순이다. 
따라서 접미사 $$i$$는 크기 1인 그룹을 이룬다.
∎   
   
> **Theorem1. (Correctness)** $$C_H$$, $$G_H$$, $$|G_H|$$가 주어졌을 때, $$P_H$$가 다음 조건을 만족하면 **Algorithm1**의 결과 $$C_{2H}^*=C_{2H}$$ 이다.   

> 1. $$P_H\in \mathbb{F}$$   
2. $$P_H[0] = N-H, P_H[1] = N-H+1, ..., P_H[H-1] = N-1$$   
3. $$C_H^{-1}(x+H)<C_H^{-1}(y+H) \implies P_H^{-1}(x)<P_H^{-1}(y)$$   
   
**Proof.**   
서로 다른 두 접미사 $$x \in \mathbb{S}$$와 $$y \in \mathbb{S}$$에 대해 일반성을 잃지 않고 $$L(x)>L(y)$$라고 하자. 
이 때 $$x\overset{\mathrm{H}}{=}y$$인 경우만 고려해도 된다. 
왜냐하면 $$x\overset{\mathrm{H}}{<}y \implies x\overset{\mathrm{2H}}{<}y$$이고,
$$x\overset{\mathrm{H}}{>}y \implies x\overset{\mathrm{2H}}{>}y$$이기 때문에 이 때는 $$P_H$$와 상관 없이 $$x$$와 $$y$$에 대해서 $$C_{2H}^*$$도 제대로 정렬되기 때문이다.     
또한 길이가 $$H$$보다 작은 접미사도 고려하지 않아도 된다. 
그 이유는 **Lemma1**에 의해 길이 $$H$$보다 짧은 접미사 $$z$$는 임의의 접미사 $$w$$에 대해 $$z\overset{\mathrm{H}}{\neq}w$$이기 때문이다. 
따라서 $$x$$와 $$y$$에 대해 $$x+H\in \mathbb{S}\land y+H\in \mathbb{S}$$임을 알 수 있다. 이제 다음의 같이 세 가지 경우를 고려하자.

**Case1.** $$x\overset{\mathrm{2H}}{=}y$$인 경우.   
$$
\begin{matrix}
x\overset{\mathrm{2H}}{=}y &\implies& x+H\overset{\mathrm{H}}{=}y+H \\
                    &\implies& C_H^{-1}(x+H)<C_H^{-1}(y+H) & (\because C_H\mbox{ is already sorted})\\
                    &\implies& P_H^{-1}(x)<P_H^{-1}(y) & (\because \mbox{ by condition 3.})\\
                    &\implies& C_{2H}^{*-1}(x)<C_{2H}^{*-1}(y)   
\end{matrix}
$$   

**Note.**  $$L(x)>L(y)$$ 이므로 $$x$$가 먼저 나오는 것이 맞는 결과이다.   

**Case2.** $$x\overset{\mathrm{2H}}{<}y$$인 경우.   
$$
\begin{matrix}
x\overset{\mathrm{2H}}{<}y &\implies& x+H\overset{\mathrm{H}}{<}y+H & (\because x\overset{\mathrm{H}}{=}y) \\
                &\implies& C_H^{-1}(x+H)<C_H^{-1}(y+H) & (\because C_H\mbox{ is already sorted})\\
                &\implies& P_H^{-1}(x)<P_H^{-1}(y) & (\because \mbox{ by condition 3.})\\
                &\implies& C_{2H}^{*-1}(x)<C_{2H}^{*-1}(y)   
\end{matrix}
$$

**Case3.** $$x\overset{\mathrm{2H}}{>}y$$인 경우.   
$$
\begin{matrix}
x\overset{\mathrm{2H}}{>}y &\implies& x+H\overset{\mathrm{H}}{>}y+H & (\because x\overset{\mathrm{H}}{=}y) \\
                &\implies& C_H^{-1}(x+H)>C_H^{-1}(y+H) & (\because C_H\mbox{ is already sorted})\\
                &\implies& P_H^{-1}(x)>P_H^{-1}(y) & (\because \mbox{ by condition 3.})\\
                &\implies& C_{2H}^{*-1}(x)>C_{2H}^{*-1}(y)   
\end{matrix}
$$    

따라서 $$C_{2H}^*=C_{2H}$$이다.
∎   

## Calculation of Priority Array 

이제 **Theorem1**의 조건을 만족하는 $$P_H$$를 계산하는 방법을 생각해 보자.

> **Algorithm2. ($$P_H$$ calculation)**   
Input: $$C_H$$   
Output: $$P_H$$   
> $$curr \gets 0$$   
> **for** $$i$$ in $$[0, H-1]$$ **do**   
$$\quad P_H[curr] \gets N-H+i$$
   
> **for** $$i$$ in $$[0, N-1]$$ **do**   
$$\quad$$**if** $$C_H[i]\geq H$$ **then**   
$$\qquad P_H[curr] \gets C_H[i]-H$$   
$$\qquad curr \gets curr+1$$   
   
이 알고리즘이 올바르게 작동한다는 사실을 다음을 통해 알 수 있다.

> **Theorem2. (Correctness)**  
**Algorithm2**의 결과로 계산된 $$P_H$$는 **Theorem1**의 조건을 만족한다.
   
**Proof.**    

1. $$P_H\in \mathbb{F}$$   
첫 번째 루프로부터 정의역의 $$[0, H-1]$$와 치역의 $$[N-H, N-1]$$가 커버된다.
$$C_H$$는 $$\mathbb{S}$$에서 $$\mathbb{S}$$로의 일대일 대응이므로 두 번째 루프로부터 치역의 $$[0, N-H-1]$$가 커버된다. 
같은 이유로 정의역의 $$[H, N-1]$$이 커버된다.
2. $$P_H[0]=N-H, P_H[1]=N-H+1, ..., P_H[H-1]=N-1$$   
첫 번째 루프로부터 2번 조건을 만족하는 것은 자명하다. 
3. $$C_H^{-1}(x+H)<C_H^{-1}(y+H) \implies P_H^{-1}(x)<P_H^{-1}(y)$$   
$$C_H(u)=x+H$$이고, $$C_H(v)=y+H$$라고 하자. 두 번째 루프에서 $$i=u$$일 때, $$curr=a$$였다고 하자. 이 때, $$P_H(a)=C_H(u)-H=x$$으로 할당 된다.
또한 $$i=v$$일 때, $$curr=b$$이고, $$P_H(b)=C_H(v)-H=y$$으로 할당되었다고 하자.
이때 주어진 조건은 다음과 같이 다시 쓸 수 있다.
$$
\begin{matrix}
C_H^{-1}(x+H)<C_H^{-1}(y+H) &\iff& u<v \\                           
                            &\implies& P_H^{-1}(x)<P_H^{-1}(y)\\
                            &\iff& a<b 
\end{matrix}
$$   
따라서 $$u<v \implies a<b$$임을 보이면 된다. $$curr$$은 할당이 일어난 후 1만큼 증가하고, $$curr=u$$일 때 할당이 일어났으므로 $$u<v \implies a<b$$이다. ∎   
    
> **Theorem3. (Complexity)**   
**Algorithm2**의 시간 복잡도는 $$O(N)$$이다.

**Proof.** ∎
   
## Calculation of 2H-Sized Group  

다음으로 $$C_{2H}$$와 $$G_H$$로부터 $$G_{2H}$$를 계산하는 방법을 생각해보자.   

> **Algorithm3. ($$G_{2H}$$ calculation)**   
Input: $$C_{2H}$$, $$G_{H}$$      
Output: $$G_{2H}$$    
> $$g \gets 0$$   
$$G_{2H}[0] \gets g$$     
$$G_{2H}[n] \gets -1$$   
**for** $$i$$ in $$[1, N-1]$$ **do**   
$$\quad$$**if** $$G_H[C_{2H}[i]]\neq G_H[C_{2H}[i-1]]\lor G_H[C_{2H}[i]+H]\neq G_H[C_{2H}[i-1]+H]$$ **then**   
$$\qquad g \gets g+1$$   
$$\quad G_{2H}[i] \gets g$$

**Note.** 이전 정렬에서 얻은 정보를 이용하여 두 접미사를 $$O(1)$$에 비교한다.   

> **Theorem4. (Correctness)**   
**Algorithm3** 은 $$G_{2H}$$를 올바르게 구한다.   
   
**Proof.**   
$$C_{2H}$$에서 인접한 두 접미사 $$x$$와 $$y$$에 대해
$$x\overset{\mathrm{H}}{\neq}y \implies x\overset{\mathrm{2H}}{\neq}y$$ 이므로 $$G_H(x)\neq G_H(y)$$이라면 $$G_{2H}(x)=G_{2H}(y)+1$$ 임을 알 수 있다. 
만약 $$x\overset{\mathrm{H}}{=}y$$라면 $$L(x)$$와 $$L(y)$$모두 $$H$$이상이므로, $$x+H$$와 $$y+H$$의 값은 최대 $$n$$이다.   
또한 모든 접미사는 서로 다르므로, $$x+H=y+H=n$$인 경우는 없다. 
따라서 $$G_{2H}[n]=-1$$으로 정의하면, $$x+H$$와 $$y+H$$를 비교함으로써 $$x$$와 $$y$$가 다른 그룹에 속하는지 여부를 알 수 있다. 
즉, $$G_H(x+H)\neq G_H(y+H) \implies G_{2H}(x)=G_{2H}(y)+1$$이다. 
따라서 **Algorithm3**은 $$G_{2H}$$를 올바르게 계산한다.
∎   

> **Theorem5. (Complexity)**   
**Algorithm3**의 시간 복잡도는 $$O(N)$$이다. 

**Proof.**∎

## Put All Together 

이상의 결과를 통해 $$O(N\log N)$$ 시간 복잡도를 가지는 접미사 배열 생성 알고리즘을 다음과 같이 작성할 수 있다.

> **Algorithm4. (Manber-Myers alg.)**   
Input: String $$S$$    
Output: Suffix array $$C$$    
Initialize $$C$$ to $$C_1$$, $$G$$ to $$G_1$$, $$|G|$$ to $$|G_1|$$    
$$H \gets 1$$   
$$g \gets 0$$   

> **while** $$H<N \land g<N$$ **do**   
$$\quad$$*//$$P_H$$ calculation*   
$$\quad curr \gets 0$$   
$$\quad$$**for** $$i$$ in $$[0, H-1]$$ **do**   
$$\qquad T[curr] \gets N-H+i$$   
$$\quad$$**for** $$i$$ in $$[0, N-1]$$ **do**   
$$\qquad$$**if** $$C[i]\geq H$$ **then**   
$$\qquad\quad T[curr] \gets C[i]-H$$   
$$\qquad\quad curr \gets curr+1$$   

> $$\quad$$*//$$C_{2H}$$ calculation*   
$$\quad C \gets radixSort(T, G, N)$$    

> $$\quad$$*//$$G_{2H}$$ calculation*   
$$\quad g \gets 0$$   
$$\quad G[0] \gets g$$     
$$\quad G[n] \gets -1$$   
$$\quad$$**for** $$i$$ in $$[1, N-1]$$ **do**   
$$\qquad$$**if** $$G[C[i]]\neq G[C[i-1]]\lor G[C[i]+H]\neq G[C[i-1]+H]$$ **then**   
$$\qquad \quad g \gets g+1$$   
$$\qquad \quad G_{2H}[i] \gets g$$   
$$\quad H \gets H \times 2$$   
   
이 알고리즘은 크게 $$P_H$$를 구하는 부분, 기수 정렬, $$G_{2H}$$를 구하는 부분으로 나뉜다. 
앞서 **Themrem1**, **Theorem2**, **Theorem4**로부터 각 부분이 올바르게 동작한다는 것을 보였다.   
초기화 부분은 $$G_1[i]=S[i]$$, $$T_1[i]=i$$로 둔 후 기수 정렬을 하면 된다. 
단, 기수 정렬은 $$\max (G)$$에 영향을 받기 때문에 이 부분에 주의해야 한다.
초기화 부분의 시간 복잡도는 $$O(N)$$이다. 
또 $$radixSort$$에서 $$\max (G_H)$$에 항상 $$N$$을 넣는데, 이는 초기화 과정의 정렬을 제외하면 $$G_H$$의 최대값이 $$N$$이기 때문이다.


> **Theorem6. (Correctness)**   
**Algorithm4**는 올바른 접미사 배열을 구한다.   

**Proof.** **Theorem1**, **Theorem2**, **Theorem4**로 부터 **while**문 내부는 올바르게 동작한다.
**while**문이 1번 실행될 때마다 $$H$$가 2배가 되므로 최대 $$\lceil log_{2} N\rceil$$ 번 실행되면 $$C=C_N$$을 구할 수 있다.∎

> **Theorem7. (Complexity)**   
**Algorithm4**는 $$O(N\log N)$$ 시간 복잡도를 가진다.   

**Proof.**   
$$C_1$$와 $$G_1$$을 계산하는 초기화 부분은 1번의 기수 정렬로 $$O(N)$$에 해결할 수 있다. 
**while**문은 최대 $$\lceil log_{2} N\rceil$$ 번 실행된다. 
그 내부는 $$O(N)$$ 시간 복잡도를 가지는 기수정렬과, $$P_H$$를 구하는 부분, 마지막으로 $$G_{2H}$$를 구하는 부분으로 나뉜다.  
각각은 **Theorem3**, **Theorem5** 으로부터 $$O(N)$$ 이므로 전체 시간 복잡도는 $$O(N\log N)$$이 된다. 
∎

