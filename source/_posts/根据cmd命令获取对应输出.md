---
title: 根据cmd命令获取对应输出
date: 2019-07-21 15:11:23
categories: 
- C++
tags:
- fsdf
---

<!-- more -->

实现函数如下：

```C++
string  MainWindow::kpCmd(string pszCmd)
{
	wstring pszCmd_w = string2wstring(pszCmd);
	wcout << "pszCmd_w is " << pszCmd_w << endl;
	// 创建匿名管道,write->read;
	SECURITY_ATTRIBUTES sa = { sizeof(SECURITY_ATTRIBUTES), NULL, TRUE };
	HANDLE hRead, hWrite;

	if (!CreatePipe(&hRead, &hWrite, &sa, 0))
	{
		cout << "@ CreatePipe failed!" << endl;
		return (" ");
	}

	cout << "@0" << endl;

	// 设置命令行进程启动信息(以隐藏方式启动命令并定位其输出到hWrite
	STARTUPINFO si = { sizeof(STARTUPINFO) }; // Pointer to STARTUPINFO structure;
	GetStartupInfo(&si);
	si.dwFlags = STARTF_USESHOWWINDOW | STARTF_USESTDHANDLES;
	si.wShowWindow = SW_HIDE; //隐藏窗口；
	si.hStdError = hWrite;
	si.hStdError = hWrite;
	si.hStdOutput = hWrite; //管道的输入端口连接命令行的输出；

	// 启动命令行
	PROCESS_INFORMATION pi;// Pointer to PROCESS_INFORMATION structure;

	if (!CreateProcess(NULL,
		(LPWSTR)pszCmd_w.c_str(),
		NULL,
		NULL,
		TRUE,
		//FALSE,          // Set handle inheritance to FALSE
		NULL,
		//0,              // No creation flags
		NULL,
		NULL,
		&si,
		&pi))
	{
		cout << "@ CreateProcess failed!" << endl;
		return ("Cannot create process");
	}

	CloseHandle(hWrite);//关闭管道的输入端口；

	// 读取命令行返回值
	string strRetTmp;
	char buff[1024] = { 0 };
	DWORD dwRead = 0;
	strRetTmp = buff;

	while (ReadFile(hRead, buff, 1024, &dwRead, NULL))//从管道的输出端获取命令行写入的数据；
	{
		cout << "buff = " << buff << endl;
		strRetTmp += buff;
	}

	CloseHandle(hRead);//关闭管道的输出端口；
	cout << "strRetTmp:" << strRetTmp << endl;
	return strRetTmp;
}
```

