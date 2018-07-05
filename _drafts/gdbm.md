
+  require 'gdbm'
+  gdbm = GDBM.new("en-it-translations.db")
+
+  text_could_be_found_in_kv_storage = gdbm[text].present?
+    gdbm.fetch(text).tap { gdbm.close }
gdbm[text] = result
gdbm.close

