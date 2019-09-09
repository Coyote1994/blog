---
title: Ukey
date: 2019-07-29 16:23:05
tags: Ukey
---


**环境**：windows10、VS2015

**客户端界面框架**：duilib 

### Ukey插拔监听

首先向windows注册监听设备插拔事件，Ukey使用的通信协议包括 USB/HID 两种。

<!-- more -->

1. 编写注册方法：

```
void MainWindow::RegisterUSBPlug()
{
	DEV_BROADCAST_DEVICEINTERFACE stDev;
	memset(&stDev, 0, sizeof(DEV_BROADCAST_DEVICEINTERFACE));
	stDev.dbcc_size = sizeof(DEV_BROADCAST_DEVICEINTERFACE);
	stDev.dbcc_devicetype = DBT_DEVTYP_DEVICEINTERFACE;
	stDev.dbcc_classguid = { 0xA5DCBF10, 0x6530, 0x11D2,{ 0x90, 0x1F, 0x00, 0xC0, 0x4F, 0xB9, 0x51, 0xED } };
	hDevNotify = RegisterDeviceNotification(this->GetHWND(), &stDev, DEVICE_NOTIFY_WINDOW_HANDLE);
}
```

2. 在 oncreate 方法中调用上面的注册函数

```
LRESULT MainWindow::OnCreate(UINT uMsg, WPARAM wParam, LPARAM lParam, BOOL& bHandled) {

    __super::OnCreate(uMsg, wParam, lParam, bHandled);
    
    // 向windows注册监听设备插拔事件
	MainWindow::RegisterUSBPlug();
	
	return 0;
}
```

3. 在 OnFinalMessage 方法中注销监听

```
void MainWindow::OnFinalMessage(HWND hWnd)
{
	__super::OnFinalMessage(hWnd);
    
    // 取消监听USB/HID插拔事件
	if (NULL != hDevNotify) {
		UnregisterDeviceNotification(hDevNotify);
		hDevNotify = NULL;
	}
}
```
4. 在 HandleMessage 方法中判断windows消息 WM_DEVICECHANGE，在接受到此消息后能获取到设备的guid，通过guid可以获取设备的pid和vid。（vid是厂商的id，每个厂商都有固定的id；pid是厂商下面具体产品的id。通过vid和pid就可以确定具体的Ukey产品）

```
LRESULT MainWindow::HandleMessage(UINT uMsg, WPARAM wParam, LPARAM lParam) {
    LRESULT lRes = 0;
	BOOL bHandled = TRUE;
    switch (uMsg) {
        case WM_DEVICECHANGE: // 设备插拔回调
	{
		DEV_BROADCAST_DEVICEINTERFACE* dbd = (DEV_BROADCAST_DEVICEINTERFACE*)lParam;
		char vid[5] = { 0 };
		char pid[5] = { 0 };

		switch (wParam)
		{
		case DBT_DEVICEREMOVECOMPLETE: // 移除 USB/HID 设备  
		{
            // 避免插拔的瞬间盘符不稳定时进行数据操作
			Sleep(100);

			MessageBoxA(NULL, L"提示", L"移除 USB/HID 设备", MB_OK);

		}
		break;
		case DBT_DEVICEARRIVAL: // 发现 USB/HID 设备  
		{
            // 避免插拔的瞬间盘符不稳定时进行数据操作
			Sleep(200);

            MessageBoxA(NULL, L"提示", L"发现 USB/HID 设备", MB_OK);
            
            // 根据获取到的 guid 获取 对应的 pid 和 vid 
			getPidAndVid(dbd->dbcc_classguid, pid, vid);
			
			// ...

		}
		break;
		default:
			break;
		}

	}
	
	//....	
	
	default:
		bHandled = FALSE; break;
	}
	if (bHandled) return lRes;
	return __super::HandleMessage(uMsg, wParam, lParam);
}
```


