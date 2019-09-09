---
title: 获取 MachineGUID
date: 2019-07-21 10:42:23
categories: 
- C++
tags:
- MachineGUID
---

<!-- more -->

```C++
string MainWindow::getMachineGUID()
{
	HKEY hKeyResult = NULL;
	HKEY hKeyResultG = NULL;
	CHAR szLocation[MAX_PATH] = { '\0' };
	CHAR szLocationG[MAX_PATH] = { '\0' };
	DWORD dwSize = 0;
	DWORD dwSizeG = 0;
	DWORD dwDataType = 0;
	DWORD dwDataTypeG = 0;
	LONG ret;
	LONG retG;
	char *lpSubKey = "SYSTEM\\CONTROLSET001\\CONTROL\\COMPUTERNAME\\ACTIVECOMPUTERNAME";
	char *KeyValue = "ComputerName";

	char *lpSubKeyG = "SOFTWARE\\MICROSOFT\\CRYPTOGRAPHY";
	char *KeyValueG = "MachineGuid";

	/************************************************************************/
	/* 功能：
	打开对应注册表项
	获取当前计算机名
	/************************************************************************/
	if (ERROR_SUCCESS == RegOpenKeyExA(HKEY_LOCAL_MACHINE, lpSubKey, 0, KEY_QUERY_VALUE, &hKeyResult))
	{
		ret = RegQueryValueExA(hKeyResult, KeyValue, 0, &dwDataType, NULL, &dwSize);
		printf("RegQueryValueEx returns %d, dwSize=%d\n", ret, dwSize);

		ret = RegQueryValueExA(hKeyResult, KeyValue, 0, &dwDataType, (LPBYTE)&szLocation, &dwSize);
		printf("RegQueryValueEx returns %d, dwSize=%d\n", ret, dwSize);

		if (ERROR_SUCCESS == ret)
		{
			printf("Location: %s\n", szLocation);
		}
		RegCloseKey(hKeyResult);
	}
	printf("----------------------------------------------------------------\n");

	/************************************************************************/
	/* 功能：
	打开对应注册表项
	获取当前计算机GUID
	/************************************************************************/
	if (ERROR_SUCCESS == RegOpenKeyExA(HKEY_LOCAL_MACHINE, lpSubKeyG, 0, KEY_QUERY_VALUE | KEY_WOW64_64KEY, &hKeyResultG))
	{
		retG = RegQueryValueExA(hKeyResultG, KeyValueG, 0, &dwDataTypeG, NULL, &dwSizeG);
		printf("RegQueryValueEx returns %d, dwSize=%d\n", retG, dwSizeG);

		retG = RegQueryValueExA(hKeyResultG, KeyValueG, 0, &dwDataTypeG, (LPBYTE)&szLocationG, &dwSizeG);
		printf("RegQueryValueEx returns %d, dwSize=%d\n", retG, dwSizeG);

		if (ERROR_SUCCESS == ret)
		{
			printf("Location: %s\n", szLocationG);
		}
		RegCloseKey(hKeyResultG);
	}
	//system("pause");

	return (string)szLocationG;
}
```


