---
title: "Toy Compiler"
date: 2017-09-05T18:34:13-04:00
draft: true
---

For a while I wanted to create a toy compiler. Just so I can boast about which
language is the best :fire: .So I took a grad level course. Few things to learn here:

  * Code generation

    Your code -> intermidiate representation -> assembly -> machine code
  * Grammars and language design

    i.e. what makes a good/bad language? Does one needs to have pointer
    arithmetics? Should language be expressive/permissive or maybe developers
    should focus and making it statically analyzable. Static vs dynamic types.
    
  I definitely answered some of the questions for myself. Just to name a few
  things that I did in this course.
  
  1. Look at IR and implement one, - namely AST
  2. Implement symbol table, and write 50+ semantic checks
  3. Traverse AST and create maching code for stack based toy
     system(Machine.java)
  4. Finish code generation part in 2 days with one other dude(thank you Nagee)
     in a 6 people final project. 
     
  I will just leave you with the
  [repository](https://github.com/terracotaPie/ToyCompiler), - there's bunch of
  pdfs with the grammar description and machine code reference sheet. This graph
  summarizes every single group project:
  ![graph](/img/graph.png)

