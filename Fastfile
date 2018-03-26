# This file contains the fastlane.tools configuration
# You can find the documentation at https://docs.fastlane.tools
#
# For a list of all available actions, check out
#
#     https://docs.fastlane.tools/actions
#

# Uncomment the line if you want fastlane to automatically update itself
# update_fastlane
# -*- coding: UTF-8 -*-

default_platform(:ios)
#测试开发包
platform :ios do
    #文件夹命名,以项目名加时间
   fileName = "IndustryLive"
    #执行每个lane之前都会执行这个
   before_each do |lane, options|
    # ...
    currentTime = Time.now.strftime("%Y-%m-%d %H-%M-%S")
    if lane == :"IndustryLive_dev"
        fileName = "IndustryLive_dev " + currentTime
    elsif lane == :"IndustryLive_appStoreTest”
        fileName = "IndustryLive_appStoreTest " + currentTime
    elsif lane == :"IndustryLive_appStore"
        fileName = "IndustryLive_appStore " + currentTime
    end
    #puts fileName
   end

  desc "来客宝项目自动打包"
  lane :IndustryLive_dev do
    gym(
	    scheme:"IndustryLive_dev“,
	    workspace:"IndustryLive.xcworkspace",
        clean:true,  # 在构建前先clean,
        configuration:"Debug",
	    export_method:"enterprise",
	    output_directory:"/Users/coder/Desktop/来客宝IPA/" + fileName,
	    output_name:"IndustryLive_dev"
        )
        	
 end

#正式上线测试包
 lane :IndustryLive_appStoreTest do
    gym(
        scheme:"IndustryLive_appStore",
        workspace:"IndustryLive.xcworkspace",
        export_method:"ad-hoc",#给注册了的机子测试用
        output_directory:"/Users/coder/Desktop/来客宝IPA/" + fileName,
        output_name:"IndustryLive_appStoreTest"
        )
 end

#正式上线appstore包
lane :IndustryLive_appStore do
    gym(
        scheme:"IndustryLive_appStore",
        workspace:"IndustryLive.xcworkspace",
        export_method:"app-store",#用于上传到appStore用
        output_directory:"/Users/coder/Desktop/来客宝IPA/" + fileName,
        output_name:"IndustryLive_appStore"
    )
 end

#全部完成之后后执行这个
 after_all do |lane, options|

    puts "开始上传蒲公英..."
    # 开始上传蒲公英
    pgyer(api_key:"4065595ad23259b34c352053506bf544",user_key:"d80860e40467f5cff3ecc366ddbcd57e")

    #向钉钉发送消息的方法
    def sendMessage(appInfor,message)
        puts '成功之后发送相关的消息通知'
        _api_key = "4065595ad23259b34c352053506bf544"
        #app 安装地址
        appInstallUrl = "https://www.pgyer.com/apiv2/app/install?_api_key=" + _api_key + "&" + "buildKey=" + appInfor["buildKey"]
        messageParam = {}
        markdown = {}
        markdown["title"] = appInfor["buildName"]
        markdown["text"] =   "### " + message + "\n\n" + "#### BuildIdentifier: " + "**" + appInfor["buildIdentifier"] + "**" + "\n\n" + "#### 上传时间: " + "**" + appInfor["buildUpdated"] + "**" + "\n\n" + '###### 版本号为: ' + "**" + appInfor["buildVersion"] + "**" + "\n\n" + '###### 最新建立版本号为: ' + "**" + appInfor["buildBuildVersion"] + "**" + "\n\n" + "![扫码安装二维码]" + "(" + appInfor["buildQRCodeURL"] + ")" + "\n\n\n" + "[查看详细内容]" + "(" + 'https://www.pgyer.com/' + appInfor["buildShortcutUrl"] +")" + "\n\n\n" + "[点击安装]" + "(" + appInstallUrl +")" + "\n\n" + ">" + "@所有人" + "\n"
        messageParam["msgtype"] = "markdown"
        messageParam["markdown"] = markdown
        at = {}
        at["isAtAll"] = true
        #at["atMobiles"] = Array["18688207482"]
        messageParam["at"] = at
        #puts messageParam
        #puts messageParam.to_json
        #调用shell脚本发送消息请求
        sh('curl','https://oapi.dingtalk.com/robot/send?access_token=0ffe808b72009374e85f7e8265a525d2af71b72ddc20aa6f921baa3c2a3705a3','-H','Content-Type: application/json','-d',messageParam.to_json)
    end

    #获取app信息的方法,把data字段的值返回
    def getAppInfor(appKey)
        puts '向蒲公英获取app的相关信息'
        pyurl = "https://www.pgyer.com/apiv2/app/view"
        pyParams = {}
        pyParams["_api_key"] = "4065595ad23259b34c352053506bf544"
        pyParams["appKey"] = appKey
        pyParams["userKey"] = "10ef063c19b0b1f5dade237c11668135"
        puri = URI.parse(pyurl)
        pres = Net::HTTP.post_form(puri, pyParams)
        resbody = JSON.parse(pres.body)
        #puts resbody
        puts "请求蒲公英信息成功"
        return resbody["data"]
    end

    appKey = "3ee199b475ac922692811b56f630789d"
    message = "来客宝测试打包成功"
    if lane == :"IndustryLive_dev"
        appKey = "3ee199b475ac922692811b56f630789d"
        message = "产业Live测试环境打包成功"
    elsif lane == :"IndustryLive_appStoreTest”
        message = "产业Live正式环境测试版本打包成功"
    elsif lane == :"IndustryLive_appStore"
        message = "产业Live正式环境上线版本打包成功"
    end
    #请求app的相关信息
    appInfor = getAppInfor appKey
    #发送钉钉群消息
    sendMessage appInfor,message

    #语音播报一下
    say message
 end

end
