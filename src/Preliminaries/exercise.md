

### Exercise A

**Exercise**: Compute $3 \cdot 4$.

<input type="text" id="answerA" placeholder="Enter your answer">
<button onclick="checkAnswerA()">Submit</button>
<p id="feedbackA"></p>

<script>
  function checkAnswerA() {
    const answer = document.getElementById("answerA").value.trim();
    const feedback = document.getElementById("feedbackA");

    if (answer === "12") {
      feedback.textContent = "✅ Correct!";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Try again.";
      feedback.style.color = "red";
    }
  }
</script>

---

### Exercise B

**Exercise**: If $x=2, y=5$, compute $(x+1)\cdot y$.

<input type="text" id="answerB" placeholder="Enter your answer">
<button onclick="checkAnswerB()">Submit</button>
<p id="feedbackB"></p>

<script>
  function checkAnswerB() {
    const answer = document.getElementById("answerB").value.trim();
    const feedback = document.getElementById("feedbackB");

    if (answer === "15") {
      feedback.textContent = "✅ Correct!";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Try again.";
      feedback.style.color = "red";
    }
  }
</script>

---

### Exercise C

**Exercise**: Fill in the blank.
The constant wire is always equal to \_\_\_.

<input type="text" id="answerC" placeholder="Enter your answer">
<button onclick="checkAnswerC()">Submit</button>
<p id="feedbackC"></p>

<script>
  function checkAnswerC() {
    const answer = document.getElementById("answerC").value.trim();
    const feedback = document.getElementById("feedbackC");

    if (answer === "1") {
      feedback.textContent = "✅ Correct!";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Hint: it's the special wire a₀.";
      feedback.style.color = "red";
    }
  }
</script>

---

### Exercise D

**Exercise**: Fill the selector table for the gate $x \cdot y = z$ at $X=7$.
Which selector is `1` for each wire?

* $u_x(7) = \_\_\_$
* $v_y(7) = \_\_\_$
* $w_z(7) = \_\_\_$

<input type="text" id="answerD" placeholder="Enter as triple like 1,1,1">
<button onclick="checkAnswerD()">Submit</button>
<p id="feedbackD"></p>

<script>
  function checkAnswerD() {
    const answer = document.getElementById("answerD").value.trim();
    const feedback = document.getElementById("feedbackD");

    if (answer === "1,1,1") {
      feedback.textContent = "✅ Correct! u_x=1, v_y=1, w_z=1.";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Remember: left=x, right=y, output=z.";
      feedback.style.color = "red";
    }
  }
</script>

---

### Exercise E

**Exercise**: If the evaluation points are $3$ and $4$, what is the target polynomial?

$$
t(X) = (X - \_\_)(X - \_\_)
$$

<input type="text" id="answerE" placeholder="Enter as two numbers like 3,4">
<button onclick="checkAnswerE()">Submit</button>
<p id="feedbackE"></p>

<script>
  function checkAnswerE() {
    const answer = document.getElementById("answerE").value.trim();
    const feedback = document.getElementById("feedbackE");

    if (answer === "3,4" || answer === "3 4") {
      feedback.textContent = "✅ Correct! t(X) = (X-3)(X-4).";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. The points are r₁=3 and r₂=4.";
      feedback.style.color = "red";
    }
  }
</script>

---

### Exercise F

**Exercise**: Fill in the blanks.

1. The constant wire is always equal to \_\_\_.
2. R1CS constraints have the form $(L\cdot a)(R\cdot a) = \_\_\_$.
3. In a QAP, divisibility by $t(X)$ guarantees that \_\_\_.

<input type="text" id="answerF" placeholder="Enter as: 1,output,all gates hold">
<button onclick="checkAnswerF()">Submit</button>
<p id="feedbackF"></p>

<script>
  function checkAnswerF() {
    const answer = document.getElementById("answerF").value.trim();
    const feedback = document.getElementById("feedbackF");

    if (answer.toLowerCase() === "1,output,all gates hold" ||
        answer.toLowerCase() === "1, o·a, all gates hold") {
      feedback.textContent = "✅ Correct!";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Expected something like: 1, output, all gates hold.";
      feedback.style.color = "red";
    }
  }
</script>

-

**Exercise**: What is `2 + 2`?

<input type="text" id="answer" placeholder="Enter your answer">
<button onclick="checkAnswer()">Submit</button>
<p id="feedback"></p>

<script>
  function checkAnswer() {
    const answer = document.getElementById("answer").value.trim();
    const feedback = document.getElementById("feedback");

    if (answer === "4") {
      feedback.textContent = "✅ Correct!";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Try again.";
      feedback.style.color = "red";
    }
  }
</script>



### Exercise A

**Exercise**: Compute $3 \cdot 4$.

<input type="text" id="answerA" placeholder="Enter your answer">
<button onclick="checkAnswerA()">Submit</button>
<p id="feedbackA"></p>

<script>
  function checkAnswerA() {
    const answer = document.getElementById("answerA").value.trim();
    const feedback = document.getElementById("feedbackA");

    if (answer === "12") {
      feedback.textContent = "✅ Correct!";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Try again.";
      feedback.style.color = "red";
    }
  }
</script>

---

### Exercise B

**Exercise**: If $x=2, y=5$, compute $(x+1)\cdot y$.

<input type="text" id="answerB" placeholder="Enter your answer">
<button onclick="checkAnswerB()">Submit</button>
<p id="feedbackB"></p>

<script>
  function checkAnswerB() {
    const answer = document.getElementById("answerB").value.trim();
    const feedback = document.getElementById("feedbackB");

    if (answer === "15") {
      feedback.textContent = "✅ Correct!";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Try again.";
      feedback.style.color = "red";
    }
  }
</script>

---

### Exercise C

**Exercise**: Fill the blank.
The constant wire is always equal to \_\_\_.

<input type="text" id="answerC" placeholder="Enter your answer">
<button onclick="checkAnswerC()">Submit</button>
<p id="feedbackC"></p>

<script>
  function checkAnswerC() {
    const answer = document.getElementById("answerC").value.trim();
    const feedback = document.getElementById("feedbackC");

    if (answer === "1") {
      feedback.textContent = "✅ Correct!";
      feedback.style.color = "green";
    } else {
      feedback.textContent = "❌ Incorrect. Hint: it's the special wire \(a_0\).";
      feedback.style.color = "red";
    }
  }
</script>
