# Introduction

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