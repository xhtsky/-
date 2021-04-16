# ai-pytemplate-serving

ai服务模板项目 for python

1、简介 -- 本模型为ai研发服务模板

2、使用步骤：

    1）修改服务app_id
       a、将run_server.py 中@app.route对应的URL/函数中的test替换本服务的app_id
       b、将test.py中REST_API_URL中的test替换成app_id,将testf.py中ocrtype = "test"的test替换成app_id
       c、将app/app_config.json 中"appId": "test"中的test替换成app_id
       
    2）搭建app
       a、仿照test_app.py 搭建服务特定的app，注意app_id 和app/app_config.json 中"appId"保持一致
       b、根据搭建的app，对app_factory.py中_known_types = {"TestApp": TestApp} 进行修改
       
    3）模型加载及使用
       a、现有的模型：densenetctc识别、densenet分类、yolo检测、psenet检测的模型加载及预测代码都已整理在nets下
       b、模型加载:
       ModelManager().load_ocr_model(self.app_config.models.capital.modelPath, self.app_config.gpu_memory_fraction)
       ModelManager().load_densenet_model(self.app_config.models.capital.modelPath, self.app_config.gpu_memory_fraction)
       ModelManager().load_yolo_model(self.app_config.models.capital.modelPath, self.app_config.gpu_memory_fraction)
       ModelManager().load_pse_model(self.app_config.models.capital.modelPath, self.app_config.gpu_memory_fraction)
       c、模型使用：
       ocr_capital = ModelManager()[self.app_config.models.capital.capital.modelPath]
       d、模型调用参数的配置：
       参考app_config.json中的models
       d、注意模型在服务中的存放
       将模型存放在model文件夹下，具体模型结构参见《3、模型规范》
      
    4）服务逻辑代码
       在start.py同级目录构建一个文件夹用于存放服务逻辑代码
       
    5）code的提交
       a、在code提交时需要在build.sh中设置，注意每次提交版本递增
        VER="1.0.0"  每次提交版本递增
        SRV_NAME="ai-sh-voucher-serving" 服务名称唯一，设定后不可改动
       b、在start.sh,restart.sh,stop.sh中将 -n=icr_template_server的icr_template_server改成服务名
       c、将各个模型按照《3、模型规范》进行归整后上传http://repo.hexinfo.cn:8000/artifactory
          将上传后的URL更新到get_model.sh中
   
3、模型规范
    
   1、对于每个models.***.modelPath中modelPath是指模型文件在model下的文件夹路径，是model的下一级目录
    
   2、每个模型文件包含model_config.json存储模型参数以及相关的pb模型(其他参数文件)
   
   3、各个模型配置
   
   a、densenetctc识别--含model_config.json,pb文件，字符文件
      
           model_config.json
           {
            "modelId": "dxje",
            "modelType": "densentctc",
            "modelName": "大写金额",
            "modelFile": "capital_rec_handwriting_print_20210111_00.pb",
            "charFile": "capital_v1_20210111.txt",
            "imageHeight": 32,
            "imageWidth": "",
            "imageLenghtDiv": 8,
            "imageChannels":3,
            "formatImage":true
            }
            
  b、densenet分类--含model_config.json,pb文件
      
        model_config.json
          {
            "modelId": "densent-classify",
            "modelType": "densent",
            "modelName": "票据朝向分类",
            "modelFile": "densent_angle_20201112_00.pb",
            "inputName": "data",
            "outputName": "prob/Softmax",
            "classes":["180","90","270","0"],
            "imageHeight": 594,
            "imageWidth": 420,
            "imageChannels":1,
            "normalizeImage":true
            }
            
  c、yolo检测--含model_config.json,pb文件，classes文件，anchor文件
      
         model_config.json
         {
          "modelId": "yolo",
          "modelType": "yolo",
          "modelName": "yolo定位票据四个关键区域",
          "modelFile": "yolo_best_6_20201229.pb",
          "modelClassesFile": "voc_classes_20201229.txt",
          "modelAnchorFile": "yolo_anchors_20201229.txt",
          "imageShape": [512, 512],
          "modelSoce": 0.6,
          "modelIOU": 0.45
           }
           
  d、psenet检测--含model_config.json和pb文件
      
         model_config.json
         {
            "modelId": "pse",
            "modelType": "psenet",
            "modelName": "psenet文本定位",
            "modelFile": "pse_mobilenetv2_20210113.pb",
            "minAreaThresh":4,
            "segMapThresh":0.8
           }
