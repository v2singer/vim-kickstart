# vim 敲门砖


# 自定义修改

## 适配WSL

```
--- a/autoload/godot.vim
+++ b/autoload/godot.vim
@@ -51,20 +51,24 @@ func! s:run_scene(scene_name) abort
     endif

     let godot_command = g:godot_executable . ' ' . shellescape(a:scene_name)
+
     let s:last_scene_run = a:scene_name
     " if there is vim-dispatch installed, use it
     " vim-dispatch can't Start application in windows neovim :(
     " https://github.com/tpope/vim-dispatch/issues/297
+    let is_wsl = filereadable('/proc/version') && system('grep -q WSL /proc/version') == 0
     if exists(':AsyncRun')
         call asyncrun#run('', {}, godot_command)
     elseif has('win32') && has('nvim')
         call system('start ' . godot_command)
-    elseif executable('cmd.exe') && !exists('$TMUX')
+    elseif executable('cmd.exe') && !exists('$TMUX') && !is_wsl
         call system('cmd.exe /c start ' . godot_command)
     elseif exists(':Spawn')
         execute 'Spawn ' . godot_command
     elseif has("mac")
         call system('open ' . godot_command)
+    elseif is_wsl
+        call system(godot_command)
     else
         call system(godot_command)
```
