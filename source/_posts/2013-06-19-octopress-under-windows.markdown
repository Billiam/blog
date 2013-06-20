---
layout: post
title: "Octopress Under Windows"
date: 2013-06-19 19:56
comments: true
categories: meta
---

When running the `setup_github_pages` task, the
ampersand in `&hellip;` can be escaped with a caret symbol.

``` diff Rakefile.rb
@@ -345,7 +345,7 @@ task :setup_github_pages, :repo do |t, args|
   mkdir deploy_dir
   cd "#{deploy_dir}" do
     system "git init"
-    system "echo 'My Octopress Page is coming soon &hellip;' > index.html"
+    system "echo 'My Octopress Page is coming soon ^&hellip;' > index.html"
     system "git add ."
     system "git commit -m \"Octopress init\""
     system "git branch -m gh-pages" unless branch == 'master'
```