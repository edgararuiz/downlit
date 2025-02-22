# handles parsing failures gracefully

    Code
      test_evaluate("1 + ", highlight = TRUE)
    Output
      <span class='r-in'>1 + </span>
      <span class='r-err co'><span class='r-pr'>#&gt;</span> <span class='error'>Error:</span> &lt;text&gt;:2:0: unexpected end of input</span>
      <span class='r-err co'><span class='r-pr'>#&gt;</span> 1: 1 + </span>
      <span class='r-err co'><span class='r-pr'>#&gt;</span>    ^</span>

# highlights when requested

    Code
      test_evaluate("1 + \n 2 + 3", highlight = TRUE)
    Output
      <span class='r-in'><span class='fl'>1</span> <span class='op'>+</span> </span>
      <span class='r-in'> <span class='fl'>2</span> <span class='op'>+</span> <span class='fl'>3</span></span>
      <span class='r-out co'><span class='r-pr'>#&gt;</span> [1] 6</span>

# handles basic cases

    Code
      test_evaluate("# comment")
    Output
      <span class='r-in'># comment</span>
    Code
      test_evaluate("message('x')")
    Output
      <span class='r-in'>message('x')</span>
      <span class='r-msg co'><span class='r-pr'>#&gt;</span> x</span>
    Code
      test_evaluate("warning('x')")
    Output
      <span class='r-in'>warning('x')</span>
      <span class='r-wrn co'><span class='r-pr'>#&gt;</span> <span class='warning'>Warning: </span>x</span>
    Code
      test_evaluate("stop('x', call. = FALSE)")
    Output
      <span class='r-in'>stop('x', call. = FALSE)</span>
      <span class='r-err co'><span class='r-pr'>#&gt;</span> <span class='error'>Error:</span> x</span>
    Code
      test_evaluate("f <- function() stop('x'); f()")
    Output
      <span class='r-in'>f &lt;- function() stop('x'); f()</span>
      <span class='r-err co'><span class='r-pr'>#&gt;</span> <span class='error'>Error in f():</span> x</span>

# each line of input gets span

    Code
      test_evaluate("1 +\n 2 +\n 3 +\n 4 +\n 5")
    Output
      <span class='r-in'>1 +</span>
      <span class='r-in'> 2 +</span>
      <span class='r-in'> 3 +</span>
      <span class='r-in'> 4 +</span>
      <span class='r-in'> 5</span>
      <span class='r-out co'><span class='r-pr'>#&gt;</span> [1] 15</span>

# output always gets trailing nl

    Code
      test_evaluate("cat(\"a\")\ncat(\"a\\n\")")
    Output
      <span class='r-in'>cat("a")</span>
      <span class='r-out co'><span class='r-pr'>#&gt;</span> a</span>
      <span class='r-in'>cat("a\n")</span>
      <span class='r-out co'><span class='r-pr'>#&gt;</span> a</span>

# combines plots as needed

    Code
      f1 <- (function() plot(1))
      f2 <- (function() lines(0:2, 0:2))
      test_evaluate("f1()\nf2()\n")
    Output
      <span class='r-in'>f1()</span>
      <span class='r-in'>f2()</span>
      <span class='r-plt img'><img src='1.png' alt='' width='10' height='10' /></span>

---

    Code
      f3 <- (function() {
        plot(1)
        plot(2)
      })
      test_evaluate("f3()")
    Output
      <span class='r-in'>f3()</span>
      <span class='r-plt img'><img src='1.png' alt='' width='10' height='10' /></span>
      <span class='r-plt img'><img src='2.png' alt='' width='10' height='10' /></span>

# handles other plots

    <span class='r-in'>f3()</span>
    <span class='r-in'>f4()</span>
    <HTML for plot 4>

# ansi escapes are translated to html

    Code
      blue <- (function(x) paste0("\033[34m", x, "\033[39m"))
      f <- (function(x) {
        cat("Output: ", blue("blue"), "\n", sep = "")
        message(paste0("Message: ", blue("blue")))
        warning(blue("blue"), call. = FALSE)
        stop(blue("blue"), call. = FALSE)
      })
      test_evaluate("f()\n")
    Output
      <span class='r-in'>f()</span>
      <span class='r-out co'><span class='r-pr'>#&gt;</span> Output: <span style='color: #0000BB;'>blue</span></span>
      <span class='r-msg co'><span class='r-pr'>#&gt;</span> Message: <span style='color: #0000BB;'>blue</span></span>
      <span class='r-wrn co'><span class='r-pr'>#&gt;</span> <span class='warning'>Warning: </span><span style='color: #0000BB;'>blue</span></span>
      <span class='r-err co'><span class='r-pr'>#&gt;</span> <span class='error'>Error:</span> <span style='color: #0000BB;'>blue</span></span>

