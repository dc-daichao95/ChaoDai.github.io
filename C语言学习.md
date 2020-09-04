# C语言学习记录

## 标识符的定义
### 标识符（identifier）用于引用变量、函数、宏、结构以及其他在C程序中所定义的对象。
#define和typedef和作用域与普通变量的作用域是一样的，影响范围依次是块作用域 > 函数作用域 > 文件作用域。

## 大小端
+ a) Little-Endian就是低位字节排放在内存的低地址端，高位字节排放在内存的高地址端。
+ b) Big-Endian就是高位字节排放在内存的低地址端，低位字节排放在内存的高地址端。
+ c) 网络字节序：TCP/IP各层协议将字节序定义为Big-Endian，因此TCP/IP协议中使用的字节序通常称之为网络字节序。
一般来说，x86 系列 CPU 都是 little-endian 的字节序，PowerPC 通常是 big-endian，网络字节顺序也是 big-endian还有的CPU 能通过跳线来设置 CPU 工作于 Little endian 还是 Big endian 模式。

对于0x12345678的存储：

小端模式：（从低字节到高字节）
地位地址 0x78 0x56 0x34 0x12 高位地址

大端模式：（从高字节到低字节）
地位地址 0x12 0x34 0x56 0x78 高位地址

htonl() htons() 从主机字节顺序转换成网络字节顺序
ntohl() ntohs() 从网络字节顺序转换为主机字节顺序

## 检测主机大小端和网络字节序
```c
#include <arpa/inet.h>
#include <stdio.h>

#define BIG (1)
#define LIT (0)

static void judgeBlock(char const *const p, int *endian);

int main()
{
	int a = 0x12345678;
	int b = 0;
	char *p = (char *) &a;
	int endian = -1;
	printf("test host endian:\r\n");
	judgeBlock(p, &endian);
	if (endian == LIT) {
		b = htonl(a);
		printf("\r\ntest net endian\r\n");
		p = (char *) &b;
		judgeBlock(p, &endian);
	}
	return 0;
}

static inline void judgeBlock(char const *const p, int *endian)
{
	if (*p == 0x12) {
		printf("big endian\r\n");
		*endian = BIG;
	} else if (*p == 0x78) {
		printf("little endian\r\n");
		*endian = LIT;
	}
}
```

```
// --driver libvirt --kernel


// struct cmd commands[] =
// {
//     { CMD_CREATE, "create", crun_command_create},
//     { CMD_DELETE, "delete", crun_command_delete},
//     { CMD_EXEC, "exec", crun_command_exec},
//     { CMD_LIST, "list", crun_command_list},
//     { CMD_KILL, "kill", crun_command_kill},
//     { CMD_PS, "ps", crun_command_ps},
//     { CMD_RUN, "run", crun_command_run},
//     { CMD_SPEC, "spec", crun_command_spec},
//     { CMD_START, "start", crun_command_start},
//     { CMD_STATE, "state", crun_command_state},
//     { CMD_UPDATE, "update", crun_command_update},
//     { CMD_PAUSE, "pause", crun_command_pause},
//     { CMD_UNPAUSE, "resume", crun_command_unpause},
//     { CMD_CHECKPOINT, "checkpoint", crun_command_checkpoint},
//     { CMD_RESTORE, "restore", crun_command_restore},
// };

// int initRuntimeCtx()
// {
//     /* TODO */ 
// }

// static error_t parseOptCtx (int key, char *arg, struct argp_state *state)
// {
//     /* TODO */
// }

// static struct argp_option options[] =
//   {
//    {"debug", OPTION_DEBUG, 0, 0, "produce verbose output", 0},
//    {"cgroup-manager", OPTION_CGROUP_MANAGER, "MANAGER", 0, "cgroup manager", 0},
//    {"systemd-cgroup", OPTION_SYSTEMD_CGROUP, 0, 0, "use systemd cgroups", 0},
//    {"log", OPTION_LOG, "FILE", 0, NULL, 0},
//    {"log-format", OPTION_LOG_FORMAT, "FORMAT", 0, NULL, 0},
//    {"root", OPTION_ROOT, "DIR",  0, NULL, 0},
//    {"rootless", OPTION_ROOT, "VALUE",  0, NULL, 0},
//    { 0, }
//   };

// static struct argp argp = { options, parse_opt, args_doc, doc, NULL, NULL, NULL };

// static struct cmd * get_command (const char *arg)
// {
//   struct commands_s *it;
//   for (it = commands; it->value; it++)
//     if (strcmp (it->name, arg) == 0)
//       return it;
//   return NULL;
// }

int main() //int argc, char* const* argv
{
  struct OCI_Config *cfg = NULL; 
  (void)VMLaunch(cfg);
  return 0;
}
```
