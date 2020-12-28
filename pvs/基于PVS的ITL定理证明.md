## 基于PVS的ITL定理证明

```
ITL_test  % [ parameters ]
		: THEORY

  BEGIN

%%%% definition of datatype expression
  exp: DATATYPE
     BEGIN
       const(n: int)		: cst?		 : exp
       svariable(sv: nat) 	: svr?		 : exp
       variable(v: nat) 	: vr?		 : exp
       nx(nxexp : exp) 		: nx?	         : exp
       fin(finexp : exp)        : fin?	         : exp
       +(aex1: exp, aex2: exp)  : plus?	         : exp
       -(sex1: exp, sex2: exp)  : min?	         : exp
       *(mex1: exp, mex2: exp)  : mult?	         : exp
     END exp


%%%% definition of datatype formula
  form : DATATYPE
      BEGIN
         importing exp
	 %FA (v1: (vr? ), f1: form)       : fa?      : form
	 %FAs (v3: (svr? ), f1fas : form) : fas?     : form
	 skip                            : skip?     : form
	 =(eqxp1: exp, eqxp2: exp)       : eqi?      : form
	 <(lexp1: exp, lexp2: exp)       : les?      : form
	 -(fn1: form)                    : inot?     : form
	 /\(fa1: form, fa2: form)        : iand?     : form
	 chop(fc1: form, fc2: form)      : chop?     : form
	 chopstar(fcs1: form)            : chopstar? : form
     END form
     
%%%% frequently used abbreviations
     
     f0,f1,f2: var form
     va,sva:var exp
     exp1,exp2: var exp
  
     T : form = (const(0)=const(0));
     F : form = -T;
     \/(f1,f2) : form = -((-f1) /\ (-f2));
     =>(f1,f2) : form = (-f1) \/ f2;
     ==(f1,f2) : form = (f1 => f2) /\ (f2 => f1);
     %TE(va,f1) : form = -FA(va,-f1);
     %TEs(sva,f1) : form = -FAs(sva,-f1);
     X(f1) : form = chop(skip,f1);
     inf : form =chop(T,F);
     finite : form = -inf;
     F(f1) : form = chop(finite,f1);
     G(f1) : form = -(F(-f1));
     wX(f1) : form = -(X(-f1));
     Di(f1) : form = chop(f1,T);
     Bi(f1) : form = -(Di(-f1));
     ife(f0, f1, f2) : form = (f0 /\ f1) \/ (-f0 /\ f2);
     asX(exp1,exp2) : form = nx(exp1) = exp2;
     more : form = X(T);
     empty : form = -more;
     fin(f1) : form = G(empty => f1);
     while(f0, f1) : form = chopstar(f0 /\ f1) /\ fin(-f0)


%%%  Definition of some parameters
     Value: TYPE = int   % state variables are of the sort integers
     SValue: TYPE = int   % static variables are of the sort integers
     Vars: TYPE = nat   % Vars the indices of state variables (infinite number)
     SVars: TYPE = nat   % SVars the indices of static variables (infinite number)
     State: TYPE = [Vars -> Value]   % State(i): the ith state variable
     SState: TYPE = [SVars -> SValue]   % SState(i): the ith static variable
     
     importing Sequ[State]


     Interval : TYPE = sequ[State]
     env : VAR SState
     sigma : VAR Interval
     IValue : TYPE = [SState,Interval -> Value]
     E1 : VAR IValue;
     x1: var Value

%%%% lenght of an expression (needed for recursive definition)
     sizeexp(e:exp) : nat =
     reduce_nat(
     (LAMBDA (i : int): 1 + abs(i) ),	    %const(n)
     (LAMBDA (i : nat): 1 + i ),     	    %svariable(sv)
     (LAMBDA (i : nat): 1 + i ),	    %variable(v)
     (LAMBDA (i : nat) : 1 + i), 	    %nx(exp1)
     (LAMBDA (i : nat) : 1 + i),            %fin(exp2)
     (LAMBDA (i, j : nat): 1 + i + j),	    %+(exp1, exp2)
     (LAMBDA (i, j : nat): 1 + i + j),	    %-(exp1, exp2)
     (LAMBDA (i, j : nat): 1 + i + j) 	    %*(exp1, exp2)
     )(e)

%%%% semantics of O(expression)
     semnx(E1)(env,sigma) : Value =
     		 lambda x1 : if infinite(sigma) then
		 		   E1(env,suf(sigma,1))=x1 elsif
				   len(sigma)>0 then
				   E1(env,sub(sigma,1,len(sigma)))=x1
				else false endif
				
%%%% semantics of fin(expression)
     semfin(E1)(env,sigma) : Value =
     			   epsilon(lambda x1 : if infinite(sigma) then
			   		  false else
			   E1(env,sub(sigma,len(sigma),len(sigma)))=x1
				endif)
    
%%%% semantics of expression
     E(e : exp)(env,sigma) : RECURSIVE Value =
     	 CASES e OF
	 const(v) : v,
	 variable(n) : seq(sigma)(0)(n),
	 svariable(n) : env(n),
	 nx(exp1) : semnx(E(exp1))(env,sigma),
	 fin(exp1) : semfin(E(exp1))(env,sigma),
	 +(exp1, exp2) : E(exp1)(env,sigma) + E(exp2)(env,sigma),
	 -(exp1, exp2) : E(exp1)(env,sigma) - E(exp2)(env,sigma),
	 *(exp1, exp2) : E(exp1)(env,sigma) * E(exp2)(env,sigma)
	 ENDCASES
      MEASURE sizeexp(e)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


    Iform : TYPE = [SState,Interval -> bool]
    F1,F2 : VAR Iform;


semantics of -f
     semnot(F1)(env,sigma) : bool = not F1(env,sigma)
     
 semantics of f1 /\ f2
     semand(F1,F2)(env,sigma) : bool = F1(env,sigma) and F2(env,sigma)
     
 semantics of f1ˆf2
     semchop(F1,F2)(env,sigma) : bool =
    	(EXISTS (m: nat):
	    ( (infinite(sigma) and F2(env,suf(sigma,m))) or
	          (not infinite(sigma) and m <= len(sigma) and
		       F2(env,sub(sigma, m, len(sigma)))
	           )
       	       )
	       and F1(env,sub(sigma, 0, m))
	    )
 	  or (infinite(sigma) and F1(env,sigma))

%%% semantics of chopstar(f)

%%%% importing theory of sequences instantiated for natural numbers
%     importing Sequ[nat]
     
%%%% definition of infinite list of chopping points
%     ininterval : TYPE = Infsequ[nat]
     %s
%%%% definiton of finite list of chopping points
%      fninterval : TYPE = Finsequ[nat]
%      il: VAR ininterval
%      fl: VAR fninterval 

%%%% semantics of chopstar(f)




%%%% lenght of an expression (needed for recursive definition)
%    sizeform(f: form) : bool =
%     reduce_nat(
%     (LAMBDA (i : int): 1 ),	    %skip
%     (LAMBDA (i : nat): 1 + i ),     	    %-(f1)
%     (LAMBDA (i, j : nat): 1 + i + j),	    %=
%     (LAMBDA (i, j : nat): 1 + i + j),	    %<
%     (LAMBDA (i, j : nat): 1 + i + j),	    %chop
%     (LAMBDA (i, j : nat): 1 + i + j) 	    %and
%     (LAMBDA (i : nat): 1 + i ),     	    %chop*
%    )(f)

%%%% semantics of formulae
%    M(f:form)(env,sigma) : RECURSIVE bool =
%    	CASES f OF
%	 FA(v,f1) : semforall(M(f1),v)(env,sigma),
%	 FAs(v,f1) : semsforall(M(f1),v)(env,sigma),	
%	 skip : (len(sigma) = 1 and not infinite(sigma)),
%	 -(f1) : semnot(M(f1))(env,sigma),
%	 =(exp1,exp2) : E(exp1)(env,sigma) = E(exp2)(env,sigma),
%	 <(exp1,exp2) : E(exp1)(env,sigma) < E(exp2)(env,sigma),
%	 chop(f1,f2) : semchop(M(f1),M(f2))(env,sigma),
%	 /\(f1,f2) : semand(M(f1),M(f2))(env,sigma),
%	 chopstar(f1) : semchopstar(M(f1))(env,sigma)
%	 ENDCASES
%       MEASURE sizeform(f)

%******************



%%%% definition of validity of formulae
%     V: pred[form] =
%     	(LAMBDA f1: (FORALL env: (FORALL sigma: M(f1)(env,sigma))))  CONVERSION V
	
%%%% the axioms
%     ChopOrImp: LEMMA (f0ˆ(f1 \/ f2)) => ((f0ˆf1) \/ (f0ˆf2))
     
%     BiBoxChopImpChop: LEMMA
%     		 (Bi(f0 => f1) /\ [](f2 => f3)) => ((f0ˆf2) => (f1ˆf3))
%%%% the rules
%     MP: LEMMA V(f0 => f1) AND V(f0) IMPLIES V(f1)
%     BoxGen: LEMMA V(f0) IMPLIES V([](f0))
%     BiGen: LEMMA V(f0) IMPLIES V(Bi(f0))


  END ITL_test

```

```
%%%% semantics of O(expression)
     semnx(E1)(env,sigma) : Value =
     		 epsilon(lambda x1 : if infinite(sigma) then
		 		   E1(env,suf(sigma,1))=x1 elsif
				   len(sigma)>0 then
				   E1(env,sub(sigma,1,len(sigma)))=x1
				else false endif)
```











> pvs代码块解析

```
    Iform : TYPE = [SState,Interval -> bool]
    F1,F2 : VAR Iform;


%%%semantics of -f
     semnot(F1)(env,sigma) : bool = not F1(env,sigma)
     
%%% semantics of f1 /\ f2
     semand(F1,F2)(env,sigma) : bool = F1(env,sigma) and F2(env,sigma)
     
%%% semantics of f1ˆf2
     semchop(F1,F2)(env,sigma) : bool =
    	(EXISTS (m: nat):
	    ( (infinite(sigma) and F2(env,suf(sigma,m))) or
	          (not infinite(sigma) and m <= len(sigma) and
		       F2(env,sub(sigma, m, len(sigma)))
	           )
       	       )
	       and F1(env,sub(sigma, 0, m))
	    )
 	  or (infinite(sigma) and F1(env,sigma))
```



```
%%% semantics of chopstar(f)

%%%% importing theory of sequences instantiated for natural numbers
     importing Sequ[nat]
     
%%%% definition of infinite list of chopping points
     ininterval : TYPE = Infsequ[nat]
     
%%%% definiton of finite list of chopping points
      fninterval : TYPE = Finsequ[nat]
      il: VAR ininterval
      fl: VAR fninterval 

%%%% semantics of chopstar(f)

semchopstar(F1)(env,sigma) : bool =
      (IF infinite(sigma) THEN
         (EXISTS fl :
	       seq(fl)(0) = 0 and
	       F1(env,suf(sigma, seq(fl)(len(fl)))) and
	       (FORALL (i: below[len(fl)]):
	           seq(fl)(i) < seq(fl)(i + 1) and
		   F1(env,sub(sigma, seq(fl)(i), seq(fl)(i + 1)))
	       )
	)
	OR
	(EXISTS il:
	   seq(il)(0) = 0 and
	   (FORALL (i: nat):
	       seq(il)(i) < seq(il)(i + 1) and
	       F1(env,sub(sigma, seq(il)(i), seq(il)(i + 1)))
	   )
        )
     ELSE
        (EXISTS fl :
	   seq(fl)(0) = 0 and
	   seq(fl)(len(fl)) = len(sigma) and
	   (FORALL (i: below[len(fl)]):
	       seq(fl)(i+1) <= len(sigma) and
	       seq(fl)(i) < seq(fl)(i + 1) and
	       F1(env,sub(sigma, seq(fl)(i), seq(fl)(i + 1)))
           )
        )
     ENDIF)


```



```
%%%% lenght of an expression (needed for recursive definition)
    sizeform(f: form) : nat =
     reduce_nat(
     % (LAMBDA (i : int): 1),	    %skip
     1,
     %(LAMBDA (i : nat): 1 + i ),     	    %-(f1)
     2,
     (LAMBDA (i, j : nat): 1 + i + j),	    %=
     (LAMBDA (i, j : nat): 1 + i + j),	    %<
     (LAMBDA (i, j : nat): 1 + i + j),	    %chop
     (LAMBDA (i, j : nat): 1 + i + j),	    %and
     (LAMBDA (i : nat): 1 + i )    	    %chop*
    )(f)

%%%% semantics of formulae
    M(f:form)(env,sigma) : RECURSIVE bool =
    	CASES f OF
	 % FA(v,f1) : semforall(M(f1),v)(env,sigma),
	 % FAs(v,f1) : semsforall(M(f1),v)(env,sigma),	
	 skip : (len(sigma) = 1 and not infinite(sigma)),
	 -(f1) : semnot(M(f1))(env,sigma),
	 =(exp1,exp2) : E(exp1)(env,sigma) = E(exp2)(env,sigma),
	 <(exp1,exp2) : E(exp1)(env,sigma) < E(exp2)(env,sigma),
	 chop(f1,f2) : semchop(M(f1),M(f2))(env,sigma),
	 /\(f1,f2) : semand(M(f1),M(f2))(env,sigma),
	 chopstar(f1) : semchopstar(M(f1))(env,sigma)
	 ENDCASES
       MEASURE sizeform(f)

```

> 代码备份

```
ITL_test  % [ parameters ]
		: THEORY

  BEGIN

%%%% syntax:definition of datatype expression
  exp: DATATYPE
     BEGIN
       const(n: int)		: cst?		 : exp
       svariable(sv: nat) 	: svr?		 : exp
       variable(v: nat) 	: vr?		 : exp
       nx(nxexp : exp) 		: nx?	         : exp
       fin(finexp : exp)        : fin?	         : exp
       +(aex1: exp, aex2: exp)  : plus?	         : exp
       -(sex1: exp, sex2: exp)  : min?	         : exp
       *(mex1: exp, mex2: exp)  : mult?	         : exp
     END exp


%%%% syntax:definition of datatype formula
  form : DATATYPE
      BEGIN
         importing exp
	 %FA (v1: (vr? ), f1: form)       : fa?      : form
	 %FAs (v3: (svr? ), fas : form) : fas?       : form    %%%Consider only propositional logic
	 skip                            : skip?     : form
	 =(eqxp1: exp, eqxp2: exp)       : eqi?      : form
	 <(lexp1: exp, lexp2: exp)       : les?      : form
	 -(fn1: form)                    : inot?     : form
	 /\(fa1: form, fa2: form)        : iand?     : form
	 chop(fc1: form, fc2: form)      : chop?     : form
	 chopstar(fcs1: form)            : chopstar? : form
     END form
     
%%%% frequently used abbreviations
     
     f0,f1,f2,f3: var form
     va,sva:var exp
     exp1,exp2: var exp
  
     T : form = (const(0)=const(0));
     F : form = -T;
     \/(f1,f2) : form = -((-f1) /\ (-f2));
     =>(f1,f2) : form = (-f1) \/ f2;
     ==(f1,f2) : form = (f1 => f2) /\ (f2 => f1);
     X(f1) : form = chop(skip,f1); 
     inf : form =chop(T,F);           %%??
     finite : form = -inf;
     F(f1) : form = chop(finite,f1);
     G(f1) : form = -(F(-f1));
     wX(f1) : form = -(X(-f1));
     Di(f1) : form = chop(f1,T);
     Bi(f1) : form = -(Di(-f1));
     %ife(f0, f1, f2) : form = (f0 /\ f1) \/ (-f0 /\ f2);   %%if expression
     asX(exp1,exp2) : form = nx(exp1) = exp2;
     more : form = X(T);
     empty : form = -more;
     fin(f1) : form = G(empty => f1);
     while(f0, f1) : form = chopstar(f0 /\ f1) /\ fin(-f0)


%%%  Definition of some parameters

     Value: TYPE = int                  % state variables are of the sort integers
     SValue: TYPE = int                 % static variables are of the sort integers
     Vars: TYPE = nat                   % Vars the indices of state variables (infinite number)
     SVars: TYPE = nat                  % SVars the indices of static variables (infinite number)
     State: TYPE = [Vars -> Value]      % State(i): the ith state variable
     SState: TYPE = [SVars -> SValue]   % SState(i): the ith static variable
     
     importing Sequ[State]


     Interval : TYPE = sequ[State]
     
     env : VAR SState
     sigma : VAR Interval
     IValue : TYPE = [SState,Interval -> Value]
     
     E1 : VAR IValue;
     x1: var Value

%%%% semantics of O(expression)
     semnx(E1)(env,sigma) : Value =
     		epsilon( lambda x1 : if infinite(sigma) then
		 		   E1(env,suf(sigma,1))=x1
		             elsif len(sigma)>0 then
				   E1(env,sub(sigma,1,len(sigma)))=x1
			     else false
			     endif)


%%%% semantics of fin(expression)
     semfin(E1)(env,sigma) : Value =
     			   epsilon(lambda x1 : if infinite(sigma) then
			   		  false else
			   E1(env,sub(sigma,len(sigma),len(sigma)))=x1
				endif)
				%
%%% lenght of an expression (needed for recursive definition)
     sizeexp(e:exp) : nat =
     reduce_nat(
     %(LAMBDA (i : int): 1 + abs(i) ),	    %const(n)
     %1,    false: Found: reals.real   Expected: [integers.int -> naturalnumbers.nat]
     (LAMBDA (i : int): 1),    %ok
     (LAMBDA (i : nat): 1 + i ),     	    %svariable(sv)
     (LAMBDA (i : nat): 1 + i ),	    %variable(v)
     (LAMBDA (i : nat) : 1 + i), 	    %nx(exp1)
     (LAMBDA (i : nat) : 1 + i),            %fin(exp2)
     (LAMBDA (i, j : nat): 1 + i + j),	    %+(exp1, exp2)
     %(LAMBDA (i, j ,k : nat): 1 + i+ j), false Found: reals.real  Expected: [[naturalnumbers.nat, naturalnumbers.nat] ->  naturalnumbers.nat]
     (LAMBDA (i, j : nat): 1 + i + j),	    %-(exp1, exp2)
     (LAMBDA (i, j : nat): 1 + i + j) 	    %*(exp1, exp2)
     )(e)
     
%%%% semantics of expression
     E(e : exp)(env,sigma) : RECURSIVE Value =
     	 CASES e OF
	 const(v)      : v,
	 variable(n)   : seq(sigma)(0)(n),
	 svariable(n)  : env(n),
	 nx(exp1)      : semnx(E(exp1))(env,sigma),
	 fin(exp1)     : semfin(E(exp1))(env,sigma),
	 +(exp1, exp2) : E(exp1)(env,sigma) + E(exp2)(env,sigma),
	 -(exp1, exp2) : E(exp1)(env,sigma) - E(exp2)(env,sigma),
	 *(exp1, exp2) : E(exp1)(env,sigma) * E(exp2)(env,sigma)
	 ENDCASES
      MEASURE sizeexp(e)




    Iform : TYPE = [SState,Interval -> bool]
    F1,F2 : VAR Iform;


%%%semantics of -f
     semnot(F1)(env,sigma) : bool = not F1(env,sigma)
     
%%% semantics of f1 /\ f2
     semand(F1,F2)(env,sigma) : bool = F1(env,sigma) and F2(env,sigma)
     
%%% semantics of f1ˆf2
     semchop(F1,F2)(env,sigma) : bool =
    	(EXISTS (m: nat):
	    ( (infinite(sigma) and F2(env,suf(sigma,m))) or
	          (not infinite(sigma) and m <= len(sigma) and
		       F2(env,sub(sigma, m, len(sigma)))
	           )
       	       )
	       and F1(env,sub(sigma, 0, m))
	    )
 	  or (infinite(sigma) and F1(env,sigma))




%%% semantics of chopstar(f)

%%%% importing theory of sequences instantiated for natural numbers
     importing Sequ[nat]
     
%%%% definition of infinite list of chopping points
     ininterval : TYPE = Infsequ[nat]
     
%%%% definiton of finite list of chopping points
      fninterval : TYPE = Finsequ[nat]
      il: VAR ininterval
      fl: VAR fninterval 

%%%% semantics of chopstar(f)

semchopstar(F1)(env,sigma) : bool =
      (IF infinite(sigma) THEN
         (EXISTS fl :
	       seq(fl)(0) = 0 and
	       F1(env,suf(sigma, seq(fl)(len(fl)))) and
	       (FORALL (i: below[len(fl)]):
	           seq(fl)(i) < seq(fl)(i + 1) and
		   F1(env,sub(sigma, seq(fl)(i), seq(fl)(i + 1)))
	       )
	)


OR
	(EXISTS il:
	   seq(il)(0) = 0 and
	   (FORALL (i: nat):
	       seq(il)(i) < seq(il)(i + 1) and
	       F1(env,sub(sigma, seq(il)(i), seq(il)(i + 1)))
	   )
        )
     ELSE
        (EXISTS fl :
	   seq(fl)(0) = 0 and
	   seq(fl)(len(fl)) = len(sigma) and
	   (FORALL (i: below[len(fl)]):
	       seq(fl)(i+1) <= len(sigma) and
	       seq(fl)(i) < seq(fl)(i + 1) and
	       F1(env,sub(sigma, seq(fl)(i), seq(fl)(i + 1)))
           )
        )
     ENDIF)


%%%% lenght of an expression (needed for recursive definition)
    sizeform(f: form) : nat =
     reduce_nat(
     % (LAMBDA (i : int): 1),	    %skip
     1,
     %(LAMBDA (i : nat): 1 + i ),     	            %-(f1)
     (LAMBDA (i,j: exp): sizeexp(i)+sizeexp(j)),
     (LAMBDA (i,j: exp): sizeexp(i)+sizeexp(j)),	    %=
     (LAMBDA (i :nat): i),	    %<
     (LAMBDA (i, j : nat): 1 + i + j),	    %chop
     (LAMBDA (i, j : nat): 1 + i + j),	    %and
     (LAMBDA (i : nat): 1 + i )    	    %chop*
    )(f)

%******************

%%%% semantics of formulae
    M(f:form)(env,sigma) : RECURSIVE bool =
    	CASES f OF
	 % FA(v,f1) : semforall(M(f1),v)(env,sigma),
	 % FAs(v,f1) : semsforall(M(f1),v)(env,sigma),	
	 skip : (len(sigma) = 1 and not infinite(sigma)),
	 -(f1) : semnot(M(f1))(env,sigma),
	 =(exp1,exp2) : E(exp1)(env,sigma) = E(exp2)(env,sigma),
	 <(exp1,exp2) : E(exp1)(env,sigma) < E(exp2)(env,sigma),

	 chop(f1,f2) : semchop(M(f1),M(f2))(env,sigma),
	 /\(f1,f2) : semand(M(f1),M(f2))(env,sigma),
	 chopstar(f1) : semchopstar(M(f1))(env,sigma)
	 ENDCASES
       MEASURE sizeform(f)


%%%% definition of validity of formulae
     V: pred[form]= 
     	(LAMBDA f1: (FORALL env: (FORALL sigma: M(f1)(env,sigma))))  CONVERSION V
	
%%%% the axioms
     ChopOrImp: LEMMA (chop(f0,(f1 \/ f2))) => (chop(f0,f1) \/ chop(f0,f2))
     
     BiBoxChopImpChop: LEMMA
     		 (Bi(f0 => f1) /\ G(f2 => f3)) => (chop(f0,f2) =>chop(f1,f3))
%%%% the rules
     MP: LEMMA V(f0 => f1) AND V(f0) IMPLIES V(f1) 
     BoxGen: LEMMA V(f0) IMPLIES V(G(f0))
     BiGen: LEMMA V(f0) IMPLIES V(Bi(f0))



     BoxChopImpChop: LEMMA G((f0 =>f1)) implies (chop(f2,f0)=>chop(f2,f1))
     RightChopImpChop: LEMMA V((f0 => f1)) implies V(((chop(f2,f0)) => (chop(f2,f1))))

  END ITL_test

```





```
     BoxChopImpChop: LEMMA G(f0 =>f1) implies chop(f2,f0)=>chop(f2,f1)
     RightChopImpChop: LEMMA V(f0 => f1) implies V(chop(f2,f0) => chop(f2,f1))
```

