# GetoptLong for mruby

An almost-straight port of GetoptLong for mruby.

```diff
--- mrblib.old/getoptlong.rb	2014-01-07 16:18:25.366898793 +0100
+++ mrblib/getoptlong.rb	2014-01-07 16:42:46.716707011 +0100
@@ -3,6 +3,8 @@
 #
 # Copyright (C) 1998, 1999, 2000  Motoyuki Kasahara.
 #
+# Patched to support mruby by Sergio Rubio <rubiojr@frameos.org>
+#
 # You may redistribute and/or modify this library under the same license
 # terms as Ruby.
 #
@@ -129,7 +131,7 @@
     #
     # Current ordering.
     #
-    if ENV.include?('POSIXLY_CORRECT')
+    if ENV['POSIXLY_CORRECT']
       @ordering = REQUIRE_ORDER
     else
       @ordering = PERMUTE
@@ -152,7 +154,7 @@
     #
     # Whether error messages are output to $stderr.
     #
-    @quiet = FALSE
+    @quiet = false
 
     #
     # Status code.
@@ -309,16 +311,16 @@
         #
         next if i == argument_flag
         begin
-          if !i.is_a?(String) || i !~ /^-([^-]|-.+)$/
+          if !i.is_a?(String) || !(i =~ /^-([^-]|-.+)$/)
             raise ArgumentError, "an invalid option `#{i}'"
           end
           if (@canonical_names.include?(i))
             raise ArgumentError, "option redefined `#{i}'"
           end
-        rescue
+        rescue => e
           @canonical_names.clear
           @argument_flags.clear
-          raise
+          raise e
         end
 
         #
@@ -359,7 +361,7 @@
     raise RuntimeError, "an error has occurred" if @error != nil
 
     @status = STATUS_TERMINATED
-    @non_option_arguments.reverse_each do |argument|
+    @non_option_arguments.reverse.each do |argument|
       ARGV.unshift(argument)
     end
 
@@ -446,7 +448,7 @@
       terminate
       return nil
     elsif @ordering == PERMUTE
-      while 0 < ARGV.length && ARGV[0] !~ /^-./
+      while 0 < ARGV.length && !(ARGV[0] =~ /^-./)
         @non_option_arguments.push(ARGV.shift)
       end
       if ARGV.length == 0
@@ -455,7 +457,7 @@
       end
       argument = ARGV.shift
     elsif @ordering == REQUIRE_ORDER
-      if (ARGV[0] !~ /^-./)
+      if !(ARGV[0] =~ /^-./)
         terminate
         return nil
       end
@@ -517,7 +519,7 @@
       elsif @argument_flags[option_name] == OPTIONAL_ARGUMENT
         if argument =~ /=(.*)$/
           option_argument = $1
-        elsif 0 < ARGV.length && ARGV[0] !~ /^-./
+        elsif 0 < ARGV.length && !(ARGV[0] =~ /^-./)
           option_argument = ARGV.shift
         else
           option_argument = ''
@@ -554,7 +556,7 @@
           if 0 < @rest_singles.length
             option_argument = @rest_singles
             @rest_singles = ''
-          elsif 0 < ARGV.length && ARGV[0] !~ /^-./
+          elsif 0 < ARGV.length && !(ARGV[0] =~ /^-./)
             option_argument = ARGV.shift
           else
             option_argument = ''
@@ -565,7 +567,7 @@
         # This is an invalid option.
         # 1003.2 specifies the format of this message.
         #
-        if ENV.include?('POSIXLY_CORRECT')
+        if ENV['POSIXLY_CORRECT']
           set_error(InvalidOption, "invalid option -- #{ch}")
         else
           set_error(InvalidOption, "invalid option -- #{ch}")


```
