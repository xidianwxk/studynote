## PVS证明

![image-20201218171418012](ITL_mix.assets\image-20201218171418012.png)

> 代码实现

```
%%%% definition of validity of formulae
     V:pred[form]= 
     	(LAMBDA f1: (FORALL (env, sigma): M(f1)(env,sigma))) % CONVERSION V

    %%%% the axioms
     ChopOrImp: LEMMA V((chop(f0,(f1 or f2))) => (chop(f0,f1) or chop(f0,f2)))
     BiBoxChopImpChop: LEMMA
     		V((Bi(f0 => f1) and G(f2 => f3)) => (chop(f0,f2) =>chop(f1,f3)))
    %%%% the rules
     MP: LEMMA V(f0 => f1) AND V(f0) IMPLIES V(f1) 
     BoxGen: LEMMA V(f0) IMPLIES V(G(f0))
     BiGen: LEMMA V(f0) IMPLIES V(Bi(f0))
```



> 证明1：

![image-20201218171427133](ITL_mix.assets\image-20201218171427133.png)

 ```
 BoxChopImpChop: LEMMA G((f0 =>f1)) implies (chop(f2,f0)=>chop(f2,f1))
 ```



![image-20201218171437460](ITL_mix.assets\image-20201218171437460.png)

```
BoxChopImpChop :  

  |-------
{1}   FORALL (f0, f1, f2: form):
        V(G(f0 => f1)) IMPLIES V(chop(f2, f0) => chop(f2, f1))

Rerunning step: (skosimp)
Skolemizing and flattening,
this simplifies to: 
BoxChopImpChop :  

{-1}  V(G(f0!1 => f1!1))
  |-------
{1}   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (lemma BiGen)
Applying BiGen 
this simplifies to: 
BoxChopImpChop :  

{-1}  FORALL (f0: form): V(f0) IMPLIES V(Bi(f0))
[-2]  V(G(f0!1 => f1!1))
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (inst -1 "f2!1=>f2!1")
Instantiating the top quantifier in -1 with the terms: 
 f2!1=>f2!1,
this simplifies to: 
BoxChopImpChop :  

{-1}  V(f2!1 => f2!1) IMPLIES V(Bi(f2!1 => f2!1))
[-2]  V(G(f0!1 => f1!1))
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (lemma BiBoxChopImpChop)
Applying BiBoxChopImpChop 
this simplifies to: 
BoxChopImpChop :  

{-1}  FORALL (f0, f1, f2, f3: form):
        V((Bi(f0 => f1) AND G(f2 => f3)) => (chop(f0, f2) => chop(f1, f3)))
[-2]  V(f2!1 => f2!1) IMPLIES V(Bi(f2!1 => f2!1))
[-3]  V(G(f0!1 => f1!1))
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (inst?)
Found substitution:
f3: form gets f1!1,
f1: form gets f2!1,
f2: form gets f0!1,
f0: form gets f2!1,
Using template: (chop(f0, f2) => chop(f1, f3))
Instantiating quantified variables,
this simplifies to: 
BoxChopImpChop :  
%%肉眼上已经得出证明结果
{-1}  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-2]  V(f2!1 => f2!1) IMPLIES V(Bi(f2!1 => f2!1))     %%%split
[-3]  V(G(f0!1 => f1!1))
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))


%%%******************改进部分*****************
Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
BoxChopImpChop.1 :  

{-1}  V(Bi(f2!1 => f2!1))
[-2]  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-3]  V(G(f0!1 => f1!1))
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (lemma V_AND_CON)
%%% V_AND_CON: LEMMA   V(f0) and V(f1)IMPLIES V(f0 and f1
Applying V_AND_CON 
this simplifies to: 
BoxChopImpChop.1 :  

{-1}  FORALL (f0, f1: form): V(f0) AND V(f1) IMPLIES V(f0 AND f1)
[-2]  V(Bi(f2!1 => f2!1))
[-3]  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-4]  V(G(f0!1 => f1!1))
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (inst?)
Found substitution:
f1: form gets G(f0!1 => f1!1),
f0: form gets Bi(f2!1 => f2!1),
Using template: f0 AND f1
Instantiating quantified variables,
this simplifies to: 
BoxChopImpChop.1 :  

{-1}  V(Bi(f2!1 => f2!1)) AND V(G(f0!1 => f1!1)) IMPLIES
       V(Bi(f2!1 => f2!1) AND G(f0!1 => f1!1))
[-2]  V(Bi(f2!1 => f2!1))
[-3]  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-4]  V(G(f0!1 => f1!1))
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (split)
Splitting conjunctions,
this yields  3 subgoals: 
BoxChopImpChop.1.1 :  

{-1}  V(Bi(f2!1 => f2!1) AND G(f0!1 => f1!1))
[-2]  V(Bi(f2!1 => f2!1))
[-3]  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-4]  V(G(f0!1 => f1!1))
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (forward-chain MP)
Forward chaining on MP,

This completes the proof of BoxChopImpChop.1.1.

BoxChopImpChop.1.2 :  

[-1]  V(Bi(f2!1 => f2!1))
[-2]  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-3]  V(G(f0!1 => f1!1))
  |-------
{1}   V(Bi(f2!1 => f2!1))
[2]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

which is trivially true.

This completes the proof of BoxChopImpChop.1.2.

BoxChopImpChop.1.3 :  

[-1]  V(Bi(f2!1 => f2!1))
[-2]  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-3]  V(G(f0!1 => f1!1))
  |-------
{1}   V(G(f0!1 => f1!1))
[2]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

which is trivially true.

This completes the proof of BoxChopImpChop.1.3.


This completes the proof of BoxChopImpChop.1.

BoxChopImpChop.2 :  

[-1]  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-2]  V(G(f0!1 => f1!1))
  |-------
{1}   V(f2!1 => f2!1)     %%%  which is trivially true？？？   
[2]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (lemma Test_1)
%%% Test_1: Lemma true implies V(f0 => f0)
Applying Test_1 
this simplifies to: 
BoxChopImpChop.2 :  

{-1}  FORALL (f0: form): TRUE IMPLIES V(f0 => f0)
[-2]  V((Bi(f2!1 => f2!1) AND G(f0!1 => f1!1)) =>
         (chop(f2!1, f0!1) => chop(f2!1, f1!1)))
[-3]  V(G(f0!1 => f1!1))
  |-------
[1]   V(f2!1 => f2!1)
[2]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (inst?)
Found substitution:
f0: form gets f2!1,
Using template: V(f0 => f0)
Instantiating quantified variables,

This completes the proof of BoxChopImpChop.2.

Q.E.D.


Run time  = 0.05 secs.
Real time = 0.23 secs.
```



> 证明2

![image-20201218171501339](ITL_mix.assets\image-20201218171501339.png)

```
RightChopImpChop: LEMMA V((f0 => f1)) implies V(((chop(f2,f0)) => (chop(f2,f1))))
```



![image-20201218171511389](ITL_mix.assets\image-20201218171511389.png)

```
RightChopImpChop :  

  |-------
{1}   FORALL (f0, f1, f2: form):
        V(f0 => f1) IMPLIES V(chop(f2, f0) => chop(f2, f1))

Rerunning step: (skosimp)    %%得到前提条件given
Skolemizing and flattening,
this simplifies to: 
RightChopImpChop :  

{-1}  V(f0!1 => f1!1)    
  |-------
{1}   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (forward-chain BoxGen)    %%引入BoxGen
Forward chaining on BoxGen,
this simplifies to: 
RightChopImpChop :  

{-1}  V(G(f0!1 => f1!1))
[-2]  V(f0!1 => f1!1)
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (lemma "BoxChopImpChop"
                 ("f0" "f0!1" "f1" "f1!1" "f2" "f2!1"))
Applying BoxChopImpChop where 
  f0 gets f0!1,
  f1 gets f1!1,
  f2 gets f2!1,
this simplifies to: 
RightChopImpChop :  

{-1}  V(G(f0!1 => f1!1)) IMPLIES V(chop(f2!1, f0!1) => chop(f2!1, f1!1))
[-2]  V(G(f0!1 => f1!1))
[-3]  V(f0!1 => f1!1)
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

Rerunning step: (split)     %%或者直接用(grind)命令
Splitting conjunctions,
this yields  2 subgoals: 
RightChopImpChop.1 :  

{-1}  V(chop(f2!1, f0!1) => chop(f2!1, f1!1))
[-2]  V(G(f0!1 => f1!1))
[-3]  V(f0!1 => f1!1)
  |-------
[1]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

which is trivially true.

This completes the proof of RightChopImpChop.1.

RightChopImpChop.2 :  

[-1]  V(G(f0!1 => f1!1))
[-2]  V(f0!1 => f1!1)
  |-------
{1}   V(G(f0!1 => f1!1))
[2]   V(chop(f2!1, f0!1) => chop(f2!1, f1!1))

which is trivially true.

This completes the proof of RightChopImpChop.2.

Q.E.D.


Run time  = 0.02 secs.
Real time = 0.08 secs.
```



> 证明3

![image-20201218171523218](ITL_mix.assets\image-20201218171523218.png)

```
 ChopOrEqv: LEMMA V(chop(f0,(f1 or f2))) <=> V(chop(f0,f1) or chop(f0,f2))
```

```
ChopOrEqv :  

  |-------
{1}   FORALL (f0, f1, f2: form):
        V(chop(f0, (f1 OR f2))) <=> V(chop(f0, f1) OR chop(f0, f2))

Rule? (skosimp)
Skolemizing and flattening,
this simplifies to: 
ChopOrEqv :  

  |-------
{1}   V(chop(f0!1, (f1!1 OR f2!1))) <=>
       V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))

Rule? (split)
Splitting conjunctions,
this yields  2 subgoals: 
ChopOrEqv.1 :  

  |-------
{1}   V(chop(f0!1, (f1!1 OR f2!1))) IMPLIES
       V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))

Rule? (postpone)
Postponing ChopOrEqv.1.

ChopOrEqv.2 :  

  |-------
{1}   V(chop(f0!1, f1!1) OR chop(f0!1, f2!1)) IMPLIES
       V(chop(f0!1, (f1!1 OR f2!1)))

Rule? 
```



+ proof 1   =>

![image-20201218171532868](ITL_mix.assets\image-20201218171532868.png)

    ```
ChopOrEqv.1 :  

  |-------
{1}   V(chop(f0!1, (f1!1 OR f2!1))) IMPLIES
       V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))

Rule? (lemma ChopOrImp)
Applying ChopOrImp 
this simplifies to: 
ChopOrEqv.1 :  

{-1}  FORALL (f0, f1, f2: form):
        V((chop(f0, (f1 OR f2))) => (chop(f0, f1) OR chop(f0, f2)))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1))) IMPLIES
       V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))

Rule? (inst?)
Found substitution:
f2: form gets f2!1,
f1: form gets f1!1,
f0: form gets f0!1,
Using template: (chop(f0, (f1 OR f2)))
Instantiating quantified variables,
this simplifies to: 
ChopOrEqv.1 :  

{-1}  V((chop(f0!1, (f1!1 OR f2!1))) =>
         (chop(f0!1, f1!1) OR chop(f0!1, f2!1)))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1))) IMPLIES
       V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))

Rule? (skosimp)
Skolemizing and flattening,
this simplifies to: 
ChopOrEqv.1 :  

[-1]  V((chop(f0!1, (f1!1 OR f2!1))) =>
         (chop(f0!1, f1!1) OR chop(f0!1, f2!1)))
{-2}  V(chop(f0!1, (f1!1 OR f2!1)))
  |-------
{1}   V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))

Rule? (forward-chain MP)       %%%或者使用命令（（lemma MP）（inst?）（grind））
Forward chaining on MP,

This completes the proof of ChopOrEqv.1.
    ```

+ proof  <=



![image-20201218171545229](ITL_mix.assets\image-20201218171545229.png)

```
ChopOrEqv.2 :  

  |-------
{1}   V(chop(f0!1, f1!1) OR chop(f0!1, f2!1)) IMPLIES
       V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (lemma RightChopImpChop
                 ("f0" "f1!1" "f1" "f1!1 or f2!1" "f2" "f0!1")) 
Applying RightChopImpChop where 
  f0 gets f1!1,
  f1 gets f1!1 OR f2!1,
  f2 gets f0!1,
this simplifies to: 
ChopOrEqv.2 :  

{-1}  V(f1!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
  |-------
[1]   V(chop(f0!1, f1!1) OR chop(f0!1, f2!1)) IMPLIES
       V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (lemma RightChopImpChop
                 ("f0" "f2!1" "f1" "f1!1 or f2!1" "f2" "f0!1")) 
Applying RightChopImpChop where 
  f0 gets f2!1,
  f1 gets f1!1 OR f2!1,
  f2 gets f0!1,
this simplifies to: 
ChopOrEqv.2 :  

{-1}  V(f2!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(f1!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
  |-------
[1]   V(chop(f0!1, f1!1) OR chop(f0!1, f2!1)) IMPLIES
       V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (skosimp)
Skolemizing and flattening,
this simplifies to: 
ChopOrEqv.2 :  
%%仍然不能直接得出结果
[-1]  V(f2!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(f1!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
{-3}  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(chop(f0!1, (f1!1 OR f2!1)))
```

> 拆分

```
Rule? (split -1)
Splitting conjunctions,
this yields  2 subgoals: 
ChopOrEqv.2.1 :  

{-1}  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(f1!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (postpone)
Postponing ChopOrEqv.2.1.

ChopOrEqv.2.2 :  

[-1]  V(f1!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(f2!1 => f1!1 OR f2!1)
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (split -2)
Splitting conjunctions,
this yields  2 subgoals: 
ChopOrEqv.2.1.1 :  

{-1}  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (postpone)
Postponing ChopOrEqv.2.1.1.

ChopOrEqv.2.1.2 :  

[-1]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(f1!1 => f1!1 OR f2!1)
[2]   V(chop(f0!1, (f1!1 OR f2!1)))
```

> 得到4个待证明的子问题

+ ChopOrEqv.2.1.1 :  

```
ChopOrEqv.2.1.1 :  

{-1}  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (lemma V_OR)
%%% V_OR: LEMMA V(f0 or f1) IMPLIES V(f0) or V(f1)  %%??感觉不太对
Applying V_OR 
this simplifies to: 
ChopOrEqv.2.1.1 :  

{-1}  FORALL (f0, f1: form): V(f0 OR f1) IMPLIES V(f0) OR V(f1)
[-2]  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-4]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (inst?)
Found substitution:
f1: form gets chop(f0!1, f2!1),
f0: form gets chop(f0!1, f1!1),
Using template: V(f0 OR f1)
Instantiating quantified variables,
this simplifies to: 
ChopOrEqv.2.1.1 :  

{-1}  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1)) IMPLIES
       V(chop(f0!1, f1!1)) OR V(chop(f0!1, f2!1))
[-2]  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-4]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (split)
Splitting conjunctions,
this yields  3 subgoals: 
ChopOrEqv.2.1.1.1 :  

{-1}  V(chop(f0!1, f1!1))
[-2]  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-4]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (forward-chain MP)
Forward chaining on MP,

This completes the proof of ChopOrEqv.2.1.1.1.

ChopOrEqv.2.1.1.2 :  

{-1}  V(chop(f0!1, f2!1))
[-2]  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-4]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (forward-chain MP)
Forward chaining on MP,

This completes the proof of ChopOrEqv.2.1.1.2.

ChopOrEqv.2.1.1.3 :  

[-1]  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

which is trivially true.

This completes the proof of ChopOrEqv.2.1.1.3.


This completes the proof of ChopOrEqv.2.1.1.
```

+ ChopOrEqv.2.1.2 : 

```
[-1]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(f1!1 => f1!1 OR f2!1)       //true  如何证明
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

%%**************grind直接证明**************
Rerunning step: (grind)      %%出现大量重写过程
OR rewrites f1!1 OR f2!1
  to NOT ((NOT f1!1) AND (NOT f2!1))
OR rewrites (NOT f1!1) OR NOT ((NOT f1!1) AND (NOT f2!1))
  to NOT ((NOT (NOT f1!1)) AND (NOT NOT ((NOT f1!1) AND (NOT f2!1))))
=> rewrites f1!1 => NOT ((NOT f1!1) AND (NOT f2!1))
  to NOT ((NOT (NOT f1!1)) AND (NOT NOT ((NOT f1!1) AND (NOT f2!1))))
semnot rewrites semnot(M(f1!1))(env, sigma)
  to NOT M(f1!1)(env, sigma)
M rewrites M((NOT f1!1))(env, sigma)
  to NOT M(f1!1)(env, sigma)
semnot rewrites semnot(M((NOT f1!1)))(env, sigma)
  to M(f1!1)(env, sigma)
...

Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of ChopOrEqv.2.1.2.


This completes the proof of ChopOrEqv.2.1

%%改进方式
%%*********引入 Test_2: Lemma true implies V(f0 => f0 or f2)******
Rule? (lemma Test_2)
Applying Test_2 
this simplifies to: 
ChopOrEqv.2.1.2 :  

{-1}  FORALL (f0, f2: form): TRUE IMPLIES V(f0 => f0 OR f2)
[-2]  V(chop(f0!1, f2!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(f1!1 => f1!1 OR f2!1)     //true  如何证明
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (inst?)
Found substitution:
f2: form gets f2!1,
f0: form gets f1!1,
Using template: V(f0 => f0 OR f2)
Instantiating quantified variables,

This completes the proof of ChopOrEqv.2.1.2.


This completes the proof of ChopOrEqv.2.1.

```



+ ChopOrEqv.2.2 :

```
ChopOrEqv.2.2 :  

[-1]  V(f1!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(f2!1 => f1!1 OR f2!1)               //true   如何证明
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

%%**************grind直接证明**************
Rerunning step: (grind)
OR rewrites f1!1 OR f2!1
  to NOT ((NOT f1!1) AND (NOT f2!1))
OR rewrites (NOT f2!1) OR NOT ((NOT f1!1) AND (NOT f2!1))
  to NOT ((NOT (NOT f2!1)) AND (NOT NOT ((NOT f1!1) AND (NOT f2!1))))
=> rewrites f2!1 => NOT ((NOT f1!1) AND (NOT f2!1))
  to NOT ((NOT (NOT f2!1)) AND (NOT NOT ((NOT f1!1) AND (NOT f2!1))))
semnot rewrites semnot(M(f2!1))(env, sigma)
  to NOT M(f2!1)(env, sigma)
M rewrites M((NOT f2!1))(env, sigma)
  to NOT M(f2!1)(env, sigma)
  ...
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of ChopOrEqv.2.2.

%%**************同2.1**************
ChopOrEqv.2.2 :  

[-1]  V(f1!1 => f1!1 OR f2!1) IMPLIES
       V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(f2!1 => f1!1 OR f2!1)       //true  如何证明
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (split)
Splitting conjunctions,
this yields  2 subgoals: 
ChopOrEqv.2.2.1 :  

{-1}  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(f2!1 => f1!1 OR f2!1)
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (postpone)
Postponing ChopOrEqv.2.2.1.

ChopOrEqv.2.2.2 :  

[-1]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(f1!1 => f1!1 OR f2!1)          //true  如何证明
[2]   V(f2!1 => f1!1 OR f2!1)          //true  如何证明
[3]   V(chop(f0!1, (f1!1 OR f2!1)))
```



+ ChopOrEqv.2.2.1 : 

```
ChopOrEqv.2.2.1 :  

{-1}  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-2]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(f2!1 => f1!1 OR f2!1)    //true
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (lemma Test_3)
%%  Test_3: Lemma true implies V(f0 => f2 or f0)
Applying Test_3 
this simplifies to: 
ChopOrEqv.2.2.1 :  

{-1}  FORALL (f0, f2: form): TRUE IMPLIES V(f0 => f2 OR f0)
[-2]  V(chop(f0!1, f1!1) => chop(f0!1, f1!1 OR f2!1))
[-3]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(f2!1 => f1!1 OR f2!1)
[2]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (inst?)
Found substitution:
f2: form gets f1!1,
f0: form gets f2!1,
Using template: V(f0 => f2 OR f0)
Instantiating quantified variables,

This completes the proof of ChopOrEqv.2.2.1.
```

+ ChopOrEqv.2.2.2 : 

```
ChopOrEqv.2.2.2 :  

[-1]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
{1}   V(f1!1 => f1!1 OR f2!1)
[2]   V(f2!1 => f1!1 OR f2!1)
[3]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (lemma Test_2)
Applying Test_2 
this simplifies to: 
ChopOrEqv.2.2.2 :  

{-1}  FORALL (f0, f2: form): TRUE IMPLIES V(f0 => f0 OR f2)
[-2]  V(chop(f0!1, f1!1) OR chop(f0!1, f2!1))
  |-------
[1]   V(f1!1 => f1!1 OR f2!1)
[2]   V(f2!1 => f1!1 OR f2!1)
[3]   V(chop(f0!1, (f1!1 OR f2!1)))

Rule? (inst?)
Found substitution:
f2: form gets f2!1,
f0: form gets f1!1,
Using template: V(f0 => f0 OR f2)
Instantiating quantified variables,

This completes the proof of ChopOrEqv.2.2.2.


This completes the proof of ChopOrEqv.2.2.


This completes the proof of ChopOrEqv.2.

Q.E.D.
```









