用reset将HEAD指向多次commit之前的那次，此时暂存区有修改，工作区并未改变，
再使用Git add . 暂存这些更改。
使用commit提交
使用Git push -f 强制刷新远程仓库的提交记录，如果之前已经提交过的话。