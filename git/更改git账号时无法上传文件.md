###### 更改git账号时可无法上传文件（git commit之前操作正常，git push就报错）
最近新注册了一个git账号，在原来电脑上使用时，每当到了git push是就显示

```
Fatal: HttpRequestException encountered.
   ▒▒▒▒▒▒▒▒ʱ▒▒▒▒
remote: Permission to 7badggb/7badggb.io.git denied to xiaweixuan.
fatal: unable to access 'https://github.com/7badggb/7badggb.io.git/': The requested URL returned error: 403
```
xiaweixuan是我之前的git账号
始终没搞明白为什么会被之前账号拒绝，翻阅众多资料后突然明白，在最早的时候，当时设置了保存密码，所以每次git push时，他会自动输入之前保存的密码，我们要做的是删除之前电脑保存的密码。

git通常有两种模式，cache模式和store。

store模式会将密码写入磁盘，永远不会过期。而cache模式则是将密码写入内存，15分钟后自动清除。

所以我们只需要将其改回cache模式，只需输入

```
git config --global credential.helper cache
```
稍等片刻，重新git push即可