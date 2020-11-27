## Suffix Array
어떤 문자열 $$S[0...n-1]$$의 모든 접미사를 사전 순으로 정렬해 둔 것. 이 때 공간 및 계산 효율을 위해 접미사 $$S[i...]$$에 대응하는 정수 $$i$$로 표현한다.

## Manber-Myers Algorithm
맨버-마이어스의 알고리즘은 접미사들의 목록을 $$1, 2, ..., 2^k$$ 글자를 기준으로 여러 번 정렬하여 접미사 배열을 구하는 알고리즘이다. 
핵심 아이디어는 $$H$$글자를 기준으로 두 문자열의 대소 관계를 알고 있을 때 $$2H$$글자 기준으로 같은 문자열들의 대소관계를 $$O(1)$$ 시간에 알 수 있다는 것이다. 
이 알고리즘의 시간복잡도는 $$O(N\log^2 N)$$ 이다. 기수 정렬을 사용하면 $$O(N\log N)$$까지 개선할 수 있다. 이 글에서는 기수 정렬을 사용한 맨버-마이어스 알고리즘을 작성하고 그 정당성과 시간복잡도를 증명한다.

## Notation
이 글에서 사용할 기호들의 의미는 다음과 같다.

| Symbol | Definition |
|---|:---|
|$$\mathbb{S}$$ | $$\mathbb{S}=[0, n-1]$$, 문자열 $$S[0...n-1]$$의 접미사들의 집합을 나타낸다. |
|$$\mathbb{F}$$ | $$\mathbb{S} \mapsto \mathbb{S}$$ 이고 일대일 대응인 함수들의 집합  |
|$$C(i)$$ | 접미사 배열의 $$i$$ 번째 값 |
|$$C^{-1}(i)$$ | $$C(x)=i$$ 일 때 $$x$$ |
|$$C_H(i)$$ | 접미사들을 앞에서부터 $$H$$ 글자를 기준으로 사전 순으로, 사전상 순서 같은 경우에는 긴 것 부터 나열했을 때, $$i$$번째 값 |
|$$C_H^{-1}(i)$$ | $$C_H(x)=i$$일 때의 $$x$$ |
|$$S_H(i)$$ | $$S[i...min(n-1,i+h-1)]$$ |
|$$G_H(i)$$ | $$\{S_H(i) | 0 \leq i \leq n-1 \}$$ 에서 $$S_H(i)$$의 사전상 순서 (0-based) |
|$$|G_H|$$ | $$|\{S_H(i) | 0 \leq i \leq n-1 \}|$$ |
|$$\overset{\underset{\mathrm{H}}{}}{=}$$| $$i\overset{\underset{\mathrm{H}}{}}{=}j \iff S_H(i)=S_H(j)$$ (lexicographically equal)|  
|$$\overset{\underset{\mathrm{H}}{}}{\neq}$$| $$i\overset{\underset{\mathrm{H}}{}}{\neq}j \iff S_H(i)<S_H(j) \lor S_H(i)>S_H(j)$$ (lexicographically not equal)|  
|$$\overset{\underset{\mathrm{H}}{}}{<}$$| $$i\overset{\underset{\mathrm{H}}{}}{<}j \iff S_H(i)<S_H(j)$$ (lexicographically less) |
|$$\overset{\underset{\mathrm{H}}{}}{>}$$| $$i\overset{\underset{\mathrm{H}}{}}{>}j \iff S_H(i)>S_H(j)$$ (lexicographically greater) |  


$$\mathbb{F}$$에 속하는 함수들은 접미사에서 접미사로 대응되는 함수로 생각할 수 있다. 예를 들어 $$C_H \in \mathbb{F}$$ 이다. 한편, $$G_H$$는 구현상의 편의를 위해 $$[0, n]\mapsto \mathbb{Z}$$ 으로 생각하자.
다음의 몇 가지 간단한 사실이 성립한다.
> **Fact1.**   
$$C=C_N=C_{2^{\lceil \log_{2} N \rceil}}$$

**Proof.**  ∎    
따라서 $$C_H$$를 구하는 과정을 최대 $$\lceil\log_{2} N\rceil$$번 수행하면 접미사 배열을 얻을 수 있다.

> **Fact2.**   
1. $$x\overset{\underset{\mathrm{H}}{}}{=}y \iff G_H(x)=G_H(y)$$   
2. $$x\overset{\underset{\mathrm{H}}{}}{<}y \iff G_H(x)<G_H(y)$$   
3. $$x\overset{\underset{\mathrm{H}}{}}{>}y \iff G_H(x)>G_H(y)$$   

**Proof.**  ∎

## Stable Radix Sort
$$O(N\log N)$$ 시간복잡도를 가지는 맨버-마이어스 알고리즘의 핵심 아이디어는 안정한 기수 정렬을 통해 $$C_H$$로부터 $$C_{2H}$$를 $$O(N)$$에 계산할 수 있다는 것이다. 
여기서 부터는 설명의 편의상 $$f:\mathbb{S}\mapsto \mathbb{S}$$인 함수 $$f$$가 마치 $$[0, n-1]$$에서 정의된 배열 $$f[0...n-1]$$인 것처럼 그 의미를 혼용하여 사용하겠다. 

> **Algorithm1. (Radix sort)**   
Input: $$T_H\in \mathbb{F}$$, $$G_H$$, $$|G_H|$$   
Output: $$C_{2H}^*\in \mathbb{F}$$   
>
> **for** $$i$$ **in** $$[0,|G_H|+1]$$ **do**   
$$\quad$$$$I[i] \gets 0$$   
   
> **for** $$i$$ **in** $$[0,n-1]$$ **do**    
$$\quad$$Increase $$I[G_H[T_H[i]]]$$ by 1   
   
> **for** $$i$$ **in** $$[1,|G_H|+1]$$ **do**   
$$\quad$$$$I[i] \gets I[i-1]+1$$   
   
> **for** $$i$$ **in** $$[0, n-1]$$ **do**   
$$\quad$$$$j \gets n-1-i$$   
$$\quad$$Decrease $$I[G_H[T_H[j]]]$$ by 1   
$$\quad$$$$C_{2H}^*[I[G_H[T_H[j]]] \gets T_H[j]$$   
   
이 알고리즘의 정당성 및 시간복잡도가 $$O(N)$$이라는 사실의 증명은 생략한다. 기수 정렬의 정의에 의해 이 알고리즘이 수행되고 나면 $$C_{2H}^*[i]$$는 $$G_H[C_{2H}^*[i]]$$가 오름 차순이 되도록 정렬된다. 만약 $$i<j$$에 대해 $$G_H[T_H[i]]=G_H[T_H[j]]$$ 라면, $$C_{2H}^{*-1}[T_H[i]]<C_{2H}^{*-1}[T_H[j]]$$가 성립한다. 
즉, 같은 $$G_H$$값을 가지게 하는 $$T_H[i]$$, $$T_H[j]$$가 있으면, $$i, j$$의 대소관계가 $$C_{2H}^*[x]=T_H[i]$$인 $$x$$와 $$C_{2H}^*[y]=T_H[j]$$인 $$y$$ 사이의 대소관계와 일치한다. 이를 안정한 정렬이라고 한다. 
한편 $$T_H$$가 특정한 성질을 가지고 있다면, $$C_{2H}^*=C_{2H}$$가 된다.

### Calculation of $$C_{2H}$$

> **Lemma1.** 문자열 $$S[0...n-1]$$에서 $$L(i)<H $$이면 $$G_H(i)=G_H(j)$$인 $$j\neq i$$는 존재하지 않는다.   
    
**Proof.**   
$$G_H(i)=G_H(j) \land j\neq i \implies L(i)\geq H \land L(j)\geq H$$ 이어서 $$L(i)<H$$라는 조건에 모순이다. 따라서 $$G_H(i)=G_H(j)$$인 $$j\neq i$$는 존재하지 않는다.∎ 
   
> **Theorem1. (Correctness)** $$C_H$$, $$G_H$$, $$|G_H|$$가 주어졌을 때, $$T_H$$가 다음 조건을 만족하면 **Algorithm1.**의 결과 $$C_{2H}^*=C_{2H}$$ 이다.
1. $$T_H\in \mathbb{F}$$
2. $$T_H[0] = N-H, T_H[1] = N-H+1, ..., T_H[H-1] = N-1$$
3. $$C_H^{-1}(x+H)<C_H^{-1}(y+H) \implies T_H^{-1}(x)<T_H^{-1}(y)$$
   
**Proof.**   
서로 다른 두 접미사 $$x \in \mathbb{S}$$와 $$y \in \mathbb{S}$$에 대해 일반성을 잃지 않고 $$L(x)>L(y)$$라고 하자.
이 때 $$x\overset{\underset{\mathrm{H}}{}}{=}y$$인 경우만 고려해도 된다. 왜냐하면 $$x\overset{\underset{\mathrm{H}}{}}{<}y \implies x\overset{\underset{\mathrm{2H}}{}}{<}y$$이고,
$$x\overset{\underset{\mathrm{H}}{}}{>}y \implies x\overset{\underset{\mathrm{2H}}{}}{>}y$$이기 때문에 이 때는 $$G_H$$가 제대로 주어져 있다면 $$x$$와 $$y$$에 대해서 $$C_{2H}^*$$도 제대로 정렬되기 때문이다.    
또한 길이가 $$H$$보다 작은 접미사도 고려하지 않아도 된다. 그 이유는 **Lemma1.**에 의해 길이 $$H$$보다 짧은 접미사 $$z$$는 임의의 접미사 $$w$$에 대해 $$z\overset{\underset{\mathrm{H}}{}}{\neq}w$$이기 때문이다. 따라서 $$x$$와 $$y$$에 대해 $$x+H\in \mathbb{S}\land y+H\in \mathbb{S}$$임을 알 수 있다. 이제 다음의 같이 세 가지 경우를 고려하자.

**Case1.** $$x\overset{\underset{\mathrm{2H}}{}}{=}y$$인 경우.   
$$
\begin{matrix}
x\overset{\underset{\mathrm{2H}}{}}{=}y &\implies& x+H\overset{\underset{\mathrm{H}}{}}{=}y+H \\
                    &\implies& C_H^{-1}(x+H)<C_H^{-1}(y+H) & (\because C_H\mbox{ is already sorted})\\
                    &\implies& T_H^{-1}(x)<T_H^{-1}(y) & (\because \mbox{ by condition 3.})\\
                    &\implies& C_{2H}^{*-1}(x)<C_{2H}^{*-1}(y)   
\end{matrix}
$$   

**Note.**  $$L(x)>L(y)$$ 이므로 $$x$$가 먼저 나오는 것이 맞는 결과이다.   

**Case2.** $$x\overset{\underset{\mathrm{2H}}{}}{<}y$$인 경우.   
$$
\begin{matrix}
x\overset{\underset{\mathrm{2H}}{}}{<}y &\implies& x+H\overset{\underset{\mathrm{H}}{}}{<}y+H & (\because x\overset{\underset{\mathrm{H}}{}}{=}y) \\
                &\implies& C_H^{-1}(x+H)<C_H^{-1}(y+H) & (\because C_H\mbox{ is already sorted})\\
                &\implies& T_H^{-1}(x)<T_H^{-1}(y) & (\because \mbox{ by condition 3.})\\
                &\implies& C_{2H}^{*-1}(x)<C_{2H}^{*-1}(y)   
\end{matrix}
$$

**Case3.** $$x\overset{\underset{\mathrm{2H}}{}}{>}y$$인 경우.   
$$
\begin{matrix}
x\overset{\underset{\mathrm{2H}}{}}{>}y &\implies& x+H\overset{\underset{\mathrm{H}}{}}{>}y+H & (\because x\overset{\underset{\mathrm{H}}{}}{=}y) \\
                &\implies& C_H^{-1}(x+H)>C_H^{-1}(y+H) & (\because C_H\mbox{ is already sorted})\\
                &\implies& T_H^{-1}(x)>T_H^{-1}(y) & (\because \mbox{ by condition 3.})\\
                &\implies& C_{2H}^{*-1}(x)>C_{2H}^{*-1}(y)   
\end{matrix}
$$    

따라서 $$C_{2H}^*=C_{2H}$$이다.∎   

### Calculation of $$T_H$$

이제 **Themrem1.**의 조건을 만족하는 $$T_H$$를 계산하는 방법을 생각해 보자.

> **Algorithm2. ($$T_H$$ calculation)**   
Input: $$C_H$$   
Output: $$T_H$$   
> $$curr \gets 0$$   
> **for** $$i$$ in $$[0, H-1]$$ **do**   
$$\quad T_H[curr] \gets N-H+i$$
   
> **for** $$i$$ in $$[0, N-1]$$ **do**   
$$\quad$$**if** $$C_H[i]\geq H$$ **then**   
$$\qquad T_H[curr] \gets C_H[i]-H$$   
$$\qquad curr \gets curr+1$$   
   
이 알고리즘이 올바르게 작동한다는 사실을 다음과 같이 알 수 있다.

> **Theorem2. (Correctness)**  
**Algorithm2.**의 결과로 계산된 $$T_H$$는 **Theorem1.**의 조건을 만족한다.
   
**Proof.**    
1. $$T_H\in \mathbb{F}$$   
첫 번째 루프로부터 정의역의 $$[0, H-1]$$와 치역의 $$[N-H, N-1]$$가 커버된다.
$$C_H$$는 $$\mathbb{S}$$에서 $$\mathbb{S}$$로의 일대일 대응이므로 두 번째 루프로부터 치역의 $$[0, N-H-1]$$가 커버된다. 
같은 이유로 정의역의 $$[H, N-1]$$이 커버된다.
   
2. $$T_H[0]=N-H, T_H[1]=N-H+1, ..., T_H[H-1]=N-1$$   
첫 번째 루프로부터 2번 조건을 만족하는 것은 자명하다. 
   
3. $$C_H^{-1}(x+H)<C_H^{-1}(y+H) \implies T_H^{-1}(x)<T_H^{-1}(y)$$   
$$C_H(u)=x+H$$이고, $$C_H(v)=y+H$$라고 하자. 두 번째 루프에서 $$i=u$$일 때, $$curr=a$$였다고 하자. 이 때, $$T_H(a)=C_H(u)-H=x$$으로 할당 된다.
또한 $$i=v$$일 때, $$curr=b$$이고, $$T_H(b)=C_H(v)-H=y$$으로 할당되었다고 하자.
이때 주어진 조건은 다음과 같이 다시 쓸 수 있다.
$$
\begin{matrix}
C_H^{-1}(x+H)<C_H^{-1}(y+H) &\iff& u<v \\                           
                            &\implies& T_H^{-1}(x)<T_H^{-1}(y)\\
                            &\iff& a<b 
\end{matrix}
$$   
따라서 $$u<v \implies a<b$$임을 보이면 된다. $$curr$$은 할당이 일어난 후 1만큼 증가하고, $$curr=u$$일 때 할당이 일어났으므로 $$u<v \implies a<b$$이다. ∎   
    
> **Theorem3. (Complexity)**   
**Algorithm2.**는 $$O(N)$$이다.

**Proof.** ∎
   
따라서 **Algorithm2.**를 통해 **Theorem1.**의 조건을 만족하는 $$T_H$$를 $$O(N)$$에 계산할 수 있다.

### Calculation of $$G_{2H}$$   
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
**Algorithm3.** 은 $$G_{2H}$$를 올바르게 구한다.   
   
**Proof.**
$$C_{2H}$$에서 인접한 두 접미사 $$x$$와 $$y$$에 대해
$$x\overset{\underset{\mathrm{H}}{}}{\neq}y \implies x\overset{\underset{\mathrm{2H}}{}}{\neq}y$$ 이므로 $$G_H(x)\neq G_H(y)$$이라면 $$G_{2H}(x)=G_{2H}(y)+1$$ 임을 알 수 있다. 
만약 $$x\overset{\underset{\mathrm{H}}{}}{=}y$$라면 $$L(x)$$와 $$L(y)$$모두 $$H$$이상이므로, $$x+H$$와 $$y+H$$의 값은 최대 $$n$$이다.   
또한 모든 접미사는 서로 다르므로, $$x+H=y+H=n$$인 경우는 없다. 따라서 $$G_{2H}[n]=-1$$으로 정의하면, $$x+H$$와 $$y+H$$를 비교함으로써 $$x$$와 $$y$$가 다른 그룹에 속하는지 여부를 알 수 있다. 즉, $$G_H(x+H)\neq G_H(y+H) \implies G_{2H}(x)=G_{2H}(y)+1$$이다. 따라서 **Algorithm3.**는 $$G_{2H}$$를 올바르게 계산한다. ∎   

> **Theorem5. (Complexity)**   
**Algorithm3.**의 시간 복잡도는 $$O(N)$$이다. 

**Proof.**∎

## Manber-Myers Algorithm

이상의 결과를 통해 $$O(N\log N)$$ 시간복잡도를 가지는 접미사 배열 생성 알고리즘을 다음과 같이 작성할 수 있다.

> **Algorithm4. (Manber-Myers alg.)**   
Input: String $$S$$    
Output: Suffix array $$C$$    
Initialize $$C$$ to $$C_1$$, $$G$$ to $$G_1$$, $$|G|$$ to $$|G_1|$$    
$$H \gets 1$$   
$$g \gets 0$$   

> **while** $$H<N \land g<N$$ **do**   
$$\quad$$*//$$T_{H}$$ calculation*   
$$\quad curr \gets 0$$   
$$\quad$$**for** $$i$$ in $$[0, H-1]$$ **do**   
$$\qquad T[curr] \gets N-H+i$$   
$$\quad$$**for** $$i$$ in $$[0, N-1]$$ **do**   
$$\qquad$$**if** $$C[i]\geq H$$ **then**   
$$\qquad\quad T[curr] \gets C[i]-H$$   
$$\qquad\quad curr \gets curr+1$$   

> $$\quad$$*//$$C_{2H}$$ calculation*   
$$\quad C \gets radix_sort(T, G, N)$$    

> $$\quad$$*//$$G_{2H}$$ calculation*   
$$\quad g \gets 0$$   
$$\quad G[0] \gets g$$     
$$\quad G[n] \gets -1$$   
$$\quad$$**for** $$i$$ in $$[1, N-1]$$ **do**   
$$\qquad$$**if** $$G[C[i]]\neq G[C[i-1]]\lor G[C[i]+H]\neq G[C[i-1]+H]$$ **then**   
$$\qquad \quad g \gets g+1$$   
$$\qquad \quad G_{2H}[i] \gets g$$   
$$\quad H \gets H \times 2$$   
   
크게 $$T_H$$를 구하는 부분, 기수 정렬, $$G_{2H}$$를 구하는 부분으로 나뉜다. 앞서 **Themrem1.**, **Theorem2.**, **Theorem4.**로부터 각 부분이 올바르게 동작한다는 것을 보였다.  
또한, $$C_1$$과 $$G_1$$은 $$G_1[i]=S[i]$$, $$T_1[i]=i$$로 둔 후 기수 정렬을 하면 $$O(N)$$에 구할 수 있다.

> **Theorem6. (Correctness)**   
**Algorithm4.**는 올바른 접미사 배열을 구한다.   

**Proof.** **Theorem1.**, **Theorem2.**, **Theorem4.**로 부터 **while**문 내부는 올바르게 동작한다.
**while**문이 최대 $$\lceil log_{2} N\rceil$$ 번 실행되고 그 후에 $$C=C_N$$을 구할 수 있다.∎

> **Theorem7. (Correctness)**   
**Algorithm4.**은 $$O(N\log N)$$ 시간복잡도를 가진다.   

**Proof.**   
**while**문이 최대 $$\lceil log_{2} N\rceil$$ 번 실행되고, 반복문 내부는 $$O(N)$$ 시간복잡도를 가지는 기수정렬과, $$T_H$$를 구하는 부분, 마지막으로 $$G_{2H}$$를 구하는 부분으로 나뉜다. 각각은 **Theorem3.**, **Theorem5.** 으로부터 $$O(N)$$ 이므로 전체 시간복잡도는 $$O(N\log N)$$이 된다. ∎
