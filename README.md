Download Link: https://assignmentchef.com/product/solved-math551-lab-10
<br>
<strong>Goal: </strong>In this lab, you will learn to use Matlab’s backslash operator for signal denoising.

<strong>Getting started: </strong>You will need to download the file m551lab10.m and put it in your working directory.

<strong>What you have to submit: </strong>The file m551lab10.m, which you will modify during the lab session.

<h1>Reminders About the Grading Software</h1>

Remember, the labs are graded with the help of software tools. If you do not follow the instructions, you will lose points. If the instructions ask you to assign a value to a variable, you must <strong>use the variable name given in the instructions</strong>. If the instructions ask you to make a variable named x1 and instead you make a variable named x or X or X1 or any name other than x1, the grading software will not find your variable and you <em>will </em>lose points. Required variables are listed in the lab instructions in a gray box like this:

<h2>Variables: x1, A, q</h2>

At times you will also be asked to answer questions in a comment in your M-file. You must <strong>format your text answers correctly</strong>. Questions that you must answer are shown in gray boxes in the lab. For example, if you see the instruction

Q7: What does the Matlab command lu do? your file must contain a line similar to the following somewhere

% Q7: It computes the LU decomposition of a matrix.

The important points about the formatting are

<ul>

 <li>The answer is on a single line.</li>

 <li>The first characters on the line is the comment character %.</li>

 <li>The answer is labeled in the form Q<em>n</em>:, where <em>n </em>stands for the question number from the gray box.</li>

</ul>

If you do not format your answers correctly, the grading software will not find them and you <em>will </em>lose points.

<h1>Basic Ideas</h1>

Consider the following figure.

This is a simulation of a noisy signal. The idea is that a broadcasting station (e.g., a radio transmitter) has sent out a smooth, time-varying signal <em>s</em>(<em>t</em>) (shown with the black curve). However, because of noise in the environment (e.g., scattering from trees, interfering signals, etc.), what we have received is a corrupted version of the signal <em>s</em><sub>cor</sub>(<em>t</em>) (the blue line). The noisy signal follows the general shape of the original signal, but also jumps up and down rapidly in time. Our job is to reconstruct as well as possible the original signal from the noisy signal by filtering out the noise.

Our first step is to convert the problem into the language of linear algebra. To do this, we will <em>discretize </em>time. That is, rather than working with the values of <em>s</em>(<em>t</em>) and <em>s</em><sub>cor</sub>(<em>t</em>) for all real numbers <em>t</em>, we’ll <em>sample s </em>and <em>s</em><sub>cor </sub>at a finite set of time values {<em>t</em><sub>1</sub><em>,t</em><sub>2</sub><em>,…,t<sub>n</sub></em>}. That allows us to represent <em>s </em>and <em>s</em><sub>cor </sub>as vectors in R<em><sup>n</sup></em>.

<em>s</em>(<em>t</em>1)

<em>s</em>(<em>t</em>2<sup>)</sup> <strong>x </strong>=  … <sub>               </sub>and        <strong>x</strong><sub>cor</sub><em>.</em>

<em>s</em>(<em>t<sub>n</sub></em>)

Our goal, then, is to use the corrupted signal <strong>x</strong><sub>cor </sub>to produce a smoothed signal <strong>y </strong>that is hopefully close to the original signal <strong>x </strong>(<strong>y </strong>≈ <strong>x</strong>). One way to do this would be to use orthogonal projection onto a suitable basis. This time, however, we’ll use a different technique, called <em>regularization</em>.

Let <em>δ &gt; </em>0 be a positive number (called the <em>regularization parameter</em>), and, for every <strong>y </strong>in R<em><sup>n</sup></em>, define

<em>n</em>−1

<em>f</em>(<strong>y</strong>) = k<strong>y </strong>− <strong>x</strong>cork2 + <em>δ</em>2 X(<em>y</em><em>i</em>+1 − <em>y</em><em>i</em>)2<em>.</em>

<em>i</em>=1

The first term in <em>f</em>(<strong>y</strong>) measures how close <strong>y </strong>is to the received signal <strong>x</strong><sub>cor</sub>; it is small for <strong>y </strong>≈ <strong>x</strong><sub>cor </sub>and large for <strong>y </strong>6≈ <strong>x</strong><sub>cor</sub>. The second term measures how “rough” <strong>y </strong>is. If each entry <em>y<sub>i</sub></em><sub>+1 </sub>is not too different from the previous <em>y<sub>i</sub></em>, the sum of squares will be small. But if there are major changes from some <em>y<sub>i </sub></em>to the next <em>y<sub>i</sub></em><sub>+1</sub>, these will cause the second term of <em>f</em>(<strong>y</strong>) to be large.

Now, consider choosing <strong>y </strong>to make <em>f</em>(<strong>y</strong>) as small as possible. By our previous analysis, we should expect the best <strong>y </strong>to strike a balance between being close to the received signal <strong>x</strong><sub>cor </sub>and being not-too-rough. The parameter <em>δ </em>quantifies the trade-off between these two goals. If <em>δ </em>is very close to 0, we won’t care too much about roughness and so <strong>y </strong>will be very close to <strong>x</strong><sub>cor</sub>. If <em>δ </em>is very very large, we will focus almost entirely on roughness, and make each value of <em>y<sub>i</sub></em><sub>+1 </sub>approximately equal to the previous:

<em>y</em><em>n </em>≈ <em>y</em><em>n</em>−1 ≈ <em>y</em><em>n</em>−2 ≈ ··· ≈ <em>y</em>2 ≈ <em>y</em>1<em>.</em>

By tuning <em>δ </em>correctly, we might hope to balance between these two extremes and find a <strong>y </strong>that’s not too rough but also not too far from <strong>x</strong><sub>cor</sub>.

Now, before we jump into the code, we only need to recognize the problem of minimizing <em>f</em>(<strong>y</strong>) as a least squares problem. To do this, we define the (<em>n </em>− 1) × <em>n </em>matrix <strong>D </strong>as

<table width="516">

 <tbody>

  <tr>

   <td rowspan="2" width="195"><sup></sup>−10<strong>D </strong>=  …0Then</td>

   <td rowspan="2" width="32">1−1…···</td>

   <td width="25">01…0</td>

   <td width="34">······…−1</td>

   <td width="43">00<sub></sub>.<sup>.</sup>.<sub></sub>1</td>

   <td width="68">so that</td>

   <td width="120"> <em>y</em>2 − <em>y</em>1   3 − <em>y</em>2  <em>y</em><strong>Dy </strong>= <sub>        </sub>…   <sub></sub><em>.</em><em>y<sub>n </sub></em>− <em>y<sub>n</sub></em>−1</td>

  </tr>

  <tr>

   <td colspan="5" width="289"><em>f</em>(<strong>y</strong>) = k<strong>y </strong>− <strong>x</strong><sub>cor</sub>k<sup>2 </sup>+ <em>δ</em><sup>2</sup>k<strong>Dy</strong>k<sup>2</sup><em>.</em></td>

  </tr>

 </tbody>

</table>

Now we will apply one final standard trick from linear algebra. (You don’t need to understand why this works for the lab, but thinking about it after the lab is highly recommended. In working out why the following is true, you will be practicing a number of important skills from this class.) If we define the (2<em>n</em>−1)×<em>n </em>matrix <strong>A </strong>and the (2<em>n </em>− 1)-vector <strong>b </strong>as

<strong>A </strong>     and     <strong>b </strong>

respectively, then

<em>f</em>(<strong>y</strong>) = k<strong>Ay </strong>− <strong>b</strong>k<sup>2</sup><em>.</em>

<h1>Tasks</h1>

<ol>

 <li>Often, when defining matrices like <strong>A </strong>above, it is helpful to begin with <em>n </em>small enough that you can actually look at the matrix and check that it is correct. The first cell of the M-file, titled “A small example” demonstrates this technique. Run the cell and observe the output in the Matlab command window. Then read the code in the cell. Notice that, since <strong>A </strong>is a sparse matrix, we are using the sparse matrix functions speye and spdiags to create <strong>A</strong>. (Remember, if you don’t know how to use a function, you can always use doc.)</li>

 <li>Now, run the cell labeled “Part 1.” It should produce a figure like the one above. To complete this cell, you need to define the variables A1 and b1, corresponding to <strong>A </strong>and <strong>b</strong>, in the places indicated. (Hint: you can copy and paste from the first cell. Just make sure to rename A and b.)</li>

</ol>

<h2>Variables: A1, b1</h2>

If you look at the documentation for Matlab’s backslash operator, you will see that, when applied to an overdetermined system, the result will be the least-squares solution. Thus, the code

% find the solution y1 = A1b1;

beneath your variable definitions produces the solution <strong>y </strong>that we’re looking for. If you have defined your variables correctly, running the cell should produce the following figure.

<ol start="3">

 <li>As you can see from the figure, the choice <em>δ </em>= 1 for this problem does filter some of the noise, but is still quite rough. Try several other values for <em>δ</em>. Notice that <em>δ </em>= 1000 is too large; the smoothed signal is essentially constant. A <em>δ </em>around 10 or 15 seems fairly good for this example.</li>

 <li>Now, let’s try a different choice of matrix <strong>D</strong>. We’ll use the (<em>n </em>− 2) × <em>n </em>matrix</li>

</ol>

1     −2      1       0      ···      0

0      1      −2      1      ···      0<sub></sub>

<strong>D </strong>= …    …       …       …       …   …<sub></sub><em>.</em>

<ul>

 <li>·· 0             1             −2           1</li>

</ul>

In the cell labeled “Part 2: Another small example,” define the variable D2 where indicated. Hint:

&gt;&gt; e = ones(5,1); full(spdiags( [e,-2*e,e], [0,1,2], 3, 5 )) ans =

<ul>

 <li>-2 1                  0                  0</li>

</ul>

0             1           -2              1              0

0             0             1           -2              1

Run the cell and make sure the output looks correct. Variables: D2

<ol start="5">

 <li>In the cell labeled “Part 3,” complete the code to solve t<strong>Math 551 Fall 2018</strong><strong>Lab 10</strong><strong>Goal: </strong>In this lab, you will learn to use Matlab’s backslash operator for signal denoising.<strong>Getting started: </strong>You will need to download the file m551lab10.m and put it in your working directory.<strong>What you have to submit: </strong>The file m551lab10.m, which you will modify during the lab session.<h1>Reminders About the Grading Software</h1>Remember, the labs are graded with the help of software tools. If you do not follow the instructions, you will lose points. If the instructions ask you to assign a value to a variable, you must <strong>use the variable name given in the instructions</strong>. If the instructions ask you to make a variable named x1 and instead you make a variable named x or X or X1 or any name other than x1, the grading software will not find your variable and you <em>will </em>lose points. Required variables are listed in the lab instructions in a gray box like this:<h2>Variables: x1, A, q</h2>At times you will also be asked to answer questions in a comment in your M-file. You must <strong>format your text answers correctly</strong>. Questions that you must answer are shown in gray boxes in the lab. For example, if you see the instructionQ7: What does the Matlab command lu do? your file must contain a line similar to the following somewhere% Q7: It computes the LU decomposition of a matrix.The important points about the formatting are

  <ul>

   <li>The answer is on a single line.</li>

   <li>The first characters on the line is the comment character %.</li>

   <li>The answer is labeled in the form Q<em>n</em>:, where <em>n </em>stands for the question number from the gray box.</li>

  </ul>If you do not format your answers correctly, the grading software will not find them and you <em>will </em>lose points.<h1>Basic Ideas</h1>Consider the following figure.This is a simulation of a noisy signal. The idea is that a broadcasting station (e.g., a radio transmitter) has sent out a smooth, time-varying signal <em>s</em>(<em>t</em>) (shown with the black curve). However, because of noise in the environment (e.g., scattering from trees, interfering signals, etc.), what we have received is a corrupted version of the signal <em>s</em><sub>cor</sub>(<em>t</em>) (the blue line). The noisy signal follows the general shape of the original signal, but also jumps up and down rapidly in time. Our job is to reconstruct as well as possible the original signal from the noisy signal by filtering out the noise.Our first step is to convert the problem into the language of linear algebra. To do this, we will <em>discretize </em>time. That is, rather than working with the values of <em>s</em>(<em>t</em>) and <em>s</em><sub>cor</sub>(<em>t</em>) for all real numbers <em>t</em>, we’ll <em>sample s </em>and <em>s</em><sub>cor </sub>at a finite set of time values {<em>t</em><sub>1</sub><em>,t</em><sub>2</sub><em>,…,t<sub>n</sub></em>}. That allows us to represent <em>s </em>and <em>s</em><sub>cor </sub>as vectors in R<em><sup>n</sup></em>.<em>s</em>(<em>t</em>1)<em>s</em>(<em>t</em>2<sup>)</sup> <strong>x </strong>=  … <sub>               </sub>and        <strong>x</strong><sub>cor</sub><em>.</em><em>s</em>(<em>t<sub>n</sub></em>)Our goal, then, is to use the corrupted signal <strong>x</strong><sub>cor </sub>to produce a smoothed signal <strong>y </strong>that is hopefully close to the original signal <strong>x </strong>(<strong>y </strong>≈ <strong>x</strong>). One way to do this would be to use orthogonal projection onto a suitable basis. This time, however, we’ll use a different technique, called <em>regularization</em>.Let <em>δ &gt; </em>0 be a positive number (called the <em>regularization parameter</em>), and, for every <strong>y </strong>in R<em><sup>n</sup></em>, define<em>n</em>−1<em>f</em>(<strong>y</strong>) = k<strong>y </strong>− <strong>x</strong>cork2 + <em>δ</em>2 X(<em>y</em><em>i</em>+1 − <em>y</em><em>i</em>)2<em>.</em><em>i</em>=1The first term in <em>f</em>(<strong>y</strong>) measures how close <strong>y </strong>is to the received signal <strong>x</strong><sub>cor</sub>; it is small for <strong>y </strong>≈ <strong>x</strong><sub>cor </sub>and large for <strong>y </strong>6≈ <strong>x</strong><sub>cor</sub>. The second term measures how “rough” <strong>y </strong>is. If each entry <em>y<sub>i</sub></em><sub>+1 </sub>is not too different from the previous <em>y<sub>i</sub></em>, the sum of squares will be small. But if there are major changes from some <em>y<sub>i </sub></em>to the next <em>y<sub>i</sub></em><sub>+1</sub>, these will cause the second term of <em>f</em>(<strong>y</strong>) to be large.Now, consider choosing <strong>y </strong>to make <em>f</em>(<strong>y</strong>) as small as possible. By our previous analysis, we should expect the best <strong>y </strong>to strike a balance between being close to the received signal <strong>x</strong><sub>cor </sub>and being not-too-rough. The parameter <em>δ </em>quantifies the trade-off between these two goals. If <em>δ </em>is very close to 0, we won’t care too much about roughness and so <strong>y </strong>will be very close to <strong>x</strong><sub>cor</sub>. If <em>δ </em>is very very large, we will focus almost entirely on roughness, and make each value of <em>y<sub>i</sub></em><sub>+1 </sub>approximately equal to the previous:<em>y</em><em>n </em>≈ <em>y</em><em>n</em>−1 ≈ <em>y</em><em>n</em>−2 ≈ ··· ≈ <em>y</em>2 ≈ <em>y</em>1<em>.</em>By tuning <em>δ </em>correctly, we might hope to balance between these two extremes and find a <strong>y </strong>that’s not too rough but also not too far from <strong>x</strong><sub>cor</sub>.Now, before we jump into the code, we only need to recognize the problem of minimizing <em>f</em>(<strong>y</strong>) as a least squares problem. To do this, we define the (<em>n </em>− 1) × <em>n </em>matrix <strong>D </strong>as

  <table width="516">

   <tbody>

    <tr>

     <td rowspan="2" width="195"><sup></sup>−10<strong>D </strong>=  …0Then</td>

     <td rowspan="2" width="32">1−1…···</td>

     <td width="25">01…0</td>

     <td width="34">······…−1</td>

     <td width="43">00<sub></sub>.<sup>.</sup>.<sub></sub>1</td>

     <td width="68">so that</td>

     <td width="120"> <em>y</em>2 − <em>y</em>1   3 − <em>y</em>2  <em>y</em><strong>Dy </strong>= <sub>        </sub>…   <sub></sub><em>.</em><em>y<sub>n </sub></em>− <em>y<sub>n</sub></em>−1</td>

    </tr>

    <tr>

     <td colspan="5" width="289"><em>f</em>(<strong>y</strong>) = k<strong>y </strong>− <strong>x</strong><sub>cor</sub>k<sup>2 </sup>+ <em>δ</em><sup>2</sup>k<strong>Dy</strong>k<sup>2</sup><em>.</em></td>

    </tr>

   </tbody>

  </table>Now we will apply one final standard trick from linear algebra. (You don’t need to understand why this works for the lab, but thinking about it after the lab is highly recommended. In working out why the following is true, you will be practicing a number of important skills from this class.) If we define the (2<em>n</em>−1)×<em>n </em>matrix <strong>A </strong>and the (2<em>n </em>− 1)-vector <strong>b </strong>as<strong>A </strong>     and     <strong>b </strong>respectively, then<em>f</em>(<strong>y</strong>) = k<strong>Ay </strong>− <strong>b</strong>k<sup>2</sup><em>.</em><h1>Tasks</h1>

  <ol>

   <li>Often, when defining matrices like <strong>A </strong>above, it is helpful to begin with <em>n </em>small enough that you can actually look at the matrix and check that it is correct. The first cell of the M-file, titled “A small example” demonstrates this technique. Run the cell and observe the output in the Matlab command window. Then read the code in the cell. Notice that, since <strong>A </strong>is a sparse matrix, we are using the sparse matrix functions speye and spdiags to create <strong>A</strong>. (Remember, if you don’t know how to use a function, you can always use doc.)</li>

   <li>Now, run the cell labeled “Part 1.” It should produce a figure like the one above. To complete this cell, you need to define the variables A1 and b1, corresponding to <strong>A </strong>and <strong>b</strong>, in the places indicated. (Hint: you can copy and paste from the first cell. Just make sure to rename A and b.)</li>

  </ol><h2>Variables: A1, b1</h2>If you look at the documentation for Matlab’s backslash operator, you will see that, when applied to an overdetermined system, the result will be the least-squares solution. Thus, the code% find the solution y1 = A1b1;beneath your variable definitions produces the solution <strong>y </strong>that we’re looking for. If you have defined your variables correctly, running the cell should produce the following figure.

  <ol start="3">

   <li>As you can see from the figure, the choice <em>δ </em>= 1 for this problem does filter some of the noise, but is still quite rough. Try several other values for <em>δ</em>. Notice that <em>δ </em>= 1000 is too large; the smoothed signal is essentially constant. A <em>δ </em>around 10 or 15 seems fairly good for this example.</li>

   <li>Now, let’s try a different choice of matrix <strong>D</strong>. We’ll use the (<em>n </em>− 2) × <em>n </em>matrix</li>

  </ol>1     −2      1       0      ···      00      1      −2      1      ···      0<sub></sub><strong>D </strong>= …    …       …       …       …   …<sub></sub><em>.</em>

  <ul>

   <li>·· 0             1             −2           1</li>

  </ul>In the cell labeled “Part 2: Another small example,” define the variable D2 where indicated. Hint:&gt;&gt; e = ones(5,1); full(spdiags( [e,-2*e,e], [0,1,2], 3, 5 )) ans =

  <ul>

   <li>-2 1                  0                  0</li>

  </ul>0             1           -2              1              00             0             1           -2              1Run the cell and make sure the output looks correct. Variables: D2

  <ol start="5">

   <li>In the cell labeled “Part 3,” complete the code to solve the problem (similar to “Part 1”) with the new choice of <strong>D</strong>. Be sure the variable names for <strong>A</strong>, <strong>b </strong>and <strong>y </strong>are A3, b3 and y3 Again, try a few choices for <em>δ</em>. When <em>δ </em>= 100, the figure should look like this:</li>

  </ol><h2>Variables: A3, b3, y3</h2>Q1: Describe the <strong>y </strong>that results when <em>δ </em>= 10<sup>5</sup>.he problem (similar to “Part 1”) with the new choice of <strong>D</strong>. Be sure the variable names for <strong>A</strong>, <strong>b </strong>and <strong>y </strong>are A3, b3 and y3 Again, try a few choices for <em>δ</em>. When <em>δ </em>= 100, the figure should look like this:</li>

</ol>

<h2>Variables: A3, b3, y3</h2>

Q1: Describe the <strong>y </strong>that results when <em>δ </em>= 10<sup>5</sup>.