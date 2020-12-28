## Executing Temporal Logic Programs

### 

### Syntax of the Logic

> **Syntax of expressions**

```
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
```

```
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
```

   ```
%%% lenght of an expression (needed for recursive definition)
     sizeexp(e:exp) : nat =
     reduce_nat(
     %(LAMBDA (i : int): 1 + abs(i) ),	    %const(n)
     %1,                         false: Found: reals.real   Expected: [integers.int -> naturalnumbers.nat]
     (LAMBDA (i : int): 1),                 %const(n)   ok
     (LAMBDA (i : nat): 1 + i ),     	    %svariable(sv)
     (LAMBDA (i : nat): 1 + i ),	        %variable(v)
     (LAMBDA (i : nat) : 1 + i), 	        %nx(exp1)
     (LAMBDA (i : nat) : 1 + i),            %fin(exp2)
     (LAMBDA (i, j : nat): 1 + i + j),	    %+(exp1, exp2)
     %(LAMBDA (i, j ,k : nat): 1 + i+ j),   false Found: reals.real  Expected: [[naturalnumbers.nat, naturalnumbers.nat] ->  naturalnumbers.nat]
     (LAMBDA (i, j : nat): 1 + i + j),	    %-(exp1, exp2)
     (LAMBDA (i, j : nat): 1 + i + j) 	    %*(exp1, exp2)
     )(e)

   ```



syntactically legal expressions:
$$
I+(\bigcirc J)+I  ,   \quad\quad\quad (\bigcirc I)+J-\bigcirc\bigcirc(I+\bigcirc J)
$$

### **Syntax of formulas**

```
form : DATATYPE
      BEGIN
         importing exp
	 skip                            : skip?     : form
	 =(eqxp1: exp, eqxp2: exp)       : eqi?      : form
	 <(lexp1: exp, lexp2: exp)       : les?      : form
	 -(fn1: form)                    : inot?     : form
	 /\(fa1: form, fa2: form)        : iand?     : form
	 chop(fc1: form, fc2: form)      : chop?     : form
	 chopstar(fcs1: form)            : chopstar? : form
     END form
     
```

```
%%%% semantics of formulae
    M(f:form)(env,sigma) : RECURSIVE bool =
    	CASES f OF	
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

    %%%% lenght of an expression (needed for recursive definition)
        sizeform(f: form) : nat =
         reduce_nat(
         %(LAMBDA (i : int): 1),   false Found: [integers.int -> reals.real]   Expected: naturalnumbers.nat  
         1,                                               %skip
         %(LAMBDA (i : nat): 1 + i ),     	              %not(form1)
         %(LAMBDA (i: exp): sizeexp(i)),  false Found: [exp -> naturalnumbers.nat]  Expected: [[exp, exp] -> naturalnumbers.nat]                 %%%-(f1)  与    -(exp1, exp2)   存在冲突？
         (LAMBDA (i,j: exp): sizeexp(i)+sizeexp(j)),      %not(form1)
         (LAMBDA (i,j: exp): sizeexp(i)+sizeexp(j)),      %=(exp1,exp2)
         (LAMBDA (i :nat): i),	                      %<(exp1,exp2)
         (LAMBDA (i, j : nat): 1 + i + j),	              %chop(form1.form2)
         (LAMBDA (i, j : nat): 1 + i + j),	              %and(form1,form2)
         (LAMBDA (i : nat): 1 + i )    	              %chop*(form)
         )(f)

syntactically legal ITL formulas:
$$
(J=2)\wedge(I=3),   \quad\quad\quad
(\bigcirc \Box[I=3])\wedge \urcorner ([\bigcirc J]=4),  \quad\quad
\bigcirc(\Box[I=3]\wedge \bigcirc \bigcirc[J=4])  \quad\quad
$$


