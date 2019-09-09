---
title: iOS framework 静态库制作并上传到 cocoapods
date: 2018-10-28 11:10:57
categories: 
- iOS
tags:
- cocoapods
---

## 前言

关于静态库和动态库：

**静态库**：以 .a 和 .framework 为文件后缀名。链接时会被完整的复制到可执行文件中，被多次使用就有多份拷贝。

**动态库**：以 .tbd 和 .framework 为文件后缀名。链接时不复制，程序运行时由系统动态加载到内存，系统只加载一次，多个程序共用（如系统的UIKit.framework等），节省内存。

## framework 静态库制作

1. 创建新工程，选择 Cocoa Touch Framework
2. 在工程中添加自己的代码 
3. 将 Target -> Build Phases -> Headers 中的 Project 中要暴露的头文件拖拽到 Pulic 里面
4. 将 target-> BuildSetting -> Mach-o Type 设为 Static Library（默认为Dynamic Library）
5. 分别选择 Generic iOS Device 和任意一个模拟器各编译一次，生成对应真机和模拟器的framework
6. 可使用下面命令查看静态库所支持的框架

	```
	lipo -info framework下的二进制文件名字
	```
7. 生成一个模拟器和真机通用的静态库

	```
lipo -create iphoneos路径下framework中二进制文件的绝对路径 iphonesimulator路径下framework中二进制文件的绝对路径 -output 所要生成的二进制文件路径
	```
然后用生成的二进制文件替换 framework 中的二进制文件

## 上传到 cocoapods

**准备工作：**

> 创建 Git 代码管理仓库并克隆到本地
> 安装 cocoapods
> 将需要上传的文件放到本地的克隆仓库并上传到 GitHub 远端仓库
> git 打上版本号 tag，并上传到服务器

### 注册 Trunk

	pod trunk register XXX@XXX.com '名字' --verbose

查看注册信息

	pod trunk me

### 配置 podspec 文件

cd 到本地仓库路径下，生成 podspec 文件

	pod spec create FrameworkTestTool
	
配置 podspec 文件，下面是我的 podspec 文件，仅供参考，详细配置信息查看官网 [Podspec Syntax Reference](http://guides.cocoapods.org/syntax/podspec.html)

```
#
#  Be sure to run `pod spec lint GMNetLib.podspec' to ensure this is a
#  valid spec and to remove all comments including this before submitting the spec.
#
#  To learn more about Podspec attributes see http://docs.cocoapods.org/specification.html
#  To see working Podspecs in the CocoaPods repo see https://github.com/CocoaPods/Specs/
#

Pod::Spec.new do |s|

  # ―――  Spec Metadata  ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  These will help people to find your library, and whilst it
  #  can feel like a chore to fill in it's definitely to your advantage. The
  #  summary should be tweet-length, and the description more in depth.
  #

  s.name         = "GMNetLib"
  s.version      = "0.1.1"
  s.summary      = "iOS 网络请求框架"

  # This description is used to generate tags and improve search results.
  #   * Think: What does it do? Why did you write it? What is the focus?
  #   * Try to keep it short, snappy and to the point.
  #   * Write the description between the DESC delimiters below.
  #   * Finally, don't worry about the indent, CocoaPods strips it!
  s.description  = <<-DESC
  基于libcurl封装的网络请求框架
                   DESC

  s.homepage     = "https://github.com/win-trust/GMNetLib.git"
  # s.screenshots  = "www.example.com/screenshots_1.gif", "www.example.com/screenshots_2.gif"


  # ―――  Spec License  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  Licensing your code is important. See http://choosealicense.com for more info.
  #  CocoaPods will detect a license file if there is a named LICENSE*
  #  Popular ones are 'MIT', 'BSD' and 'Apache License, Version 2.0'.
  #

  s.license      = { :type => "MIT", :file => "LICENSE" }
  # s.license      = { :type => "MIT", :file => "FILE_LICENSE" }


  # ――― Author Metadata  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  Specify the authors of the library, with email addresses. Email addresses
  #  of the authors are extracted from the SCM log. E.g. $ git log. CocoaPods also
  #  accepts just a name if you'd rather not provide an email address.
  #
  #  Specify a social_media_url where others can refer to, for example a twitter
  #  profile URL.
  #

  s.author             = { "LiuKunpeng" => "1219440050@qq.com" }
  # Or just: s.author    = "LiuKunpeng"
  # s.authors            = { "LiuKunpeng" => "1219440050@qq.com" }
  # s.social_media_url   = "http://twitter.com/LiuKunpeng"

  # ――― Platform Specifics ――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  If this Pod runs only on iOS or OS X, then specify the platform and
  #  the deployment target. You can optionally include the target after the platform.
  #

  s.platform     = :ios
  # s.platform     = :ios, "5.0"

  #  When using multiple platforms
  s.ios.deployment_target = "8.0"
  # s.osx.deployment_target = "10.7"
  # s.watchos.deployment_target = "2.0"
  # s.tvos.deployment_target = "9.0"


  # ――― Source Location ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  Specify the location from where the source should be retrieved.
  #  Supports git, hg, bzr, svn and HTTP.
  #

  s.source       = { :git => "https://github.com/win-trust/GMNetLib.git", :tag => "#{s.version}" }


  # ――― Source Code ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  CocoaPods is smart about how it includes source code. For source files
  #  giving a folder will include any swift, h, m, mm, c & cpp files.
  #  For header files it will include any header in the folder.
  #  Not including the public_header_files will make all headers public.
  #

  s.source_files  = "GMNetLib.framework/Headers/*.{h,m}"
  # s.exclude_files = "Classes/Exclude"

  # s.public_header_files = "Classes/**/*.h"


  # ――― Resources ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  A list of resources included with the Pod. These are copied into the
  #  target bundle with a build phase script. Anything else will be cleaned.
  #  You can preserve files from being cleaned, please don't preserve
  #  non-essential files like tests, examples and documentation.
  #

  # s.resource  = "icon.png"
  # s.resources = "Resources/*.png"

  # s.preserve_paths = "FilesToSave", "MoreFilesToSave"


  # ――― Project Linking ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  Link your library with frameworks, or libraries. Libraries do not include
  #  the lib prefix of their name.
  #

  # s.framework  = "SomeFramework"
  s.frameworks = "UIKit", "Foundation"

  # s.library   = "iconv"
  s.libraries = "c++", "z"


  # ――― Project Settings ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
  #
  #  If your library depends on compiler flags you can set them in the xcconfig hash
  #  where they will only apply to your library. If you depend on other Podspecs
  #  you can include multiple dependencies to ensure it works.

  s.requires_arc = true

  # s.xcconfig = { "HEADER_SEARCH_PATHS" => "$(SDKROOT)/usr/include/libxml2" }
  # s.dependency "JSONKit", "~> 1.4"

end

```
配置完成后进行验证

	pod spec lint

此过程会有很多细节问题，自行 google 解决即可。

### 提交到 cocoapods

	pod trunk push ./GMNetLib.podspec
	
再次使用 pod trunk me 查看是否上传成功
	
	


