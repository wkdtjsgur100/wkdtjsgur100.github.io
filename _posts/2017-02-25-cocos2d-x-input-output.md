---
layout: post
section-type: post
title: (Cocos2d-x) Vector 데이터 File I/O
category: cocos2d-x
tags: [ 'cocos2d-x', 'I/O' ]
---

cocos2d-x에서 vector 데이터를 입력/출력하기 위해서,  
fwrite를 사용하려고도 해봤지만 뭔가 복잡해지게 되고 잘 안됐었다.  
그래서 난 cocos2d-x에서 기본적으로 제공해주는 함수인

``` cpp
  FileUtils::getInstance()->writeDataToFile(data, fullPath);
  FileUtils::getInstance()->getDataFromFile(fullPath);
```

를 이용해서 Vector 데이터들을 컨트롤 해봤다.

### File Input Example

``` cpp
    std::vector<DataType> newInfos;

    newInfos.push_back(DataType(1,5));
    newInfos.push_back(DataType(3,4));

    std::string writablePath = FileUtils::getInstance()->getWritablePath(); //플랫폼 별 리소스 기본 경로를 받아온다.
    std::string fileName = "info.csv";

    std::string fullPath = writablePath + fileName;

    Data data;

    data.copy((unsigned char*) newInfos.data(), newInfos.size() * sizeof(DataType));

    FileUtils::getInstance()->writeDataToFile(data, fullPath);
```

여기서 DataType은 자료형(int,std::string,struct,class 등)를 의미한다.

### File Output Example:

``` cpp
    std::string writablePath = FileUtils::getInstance()->getWritablePath();
    std::string fileName = "info.csv";

    std::string fullPath = writablePath + fileName;

    std::string readDataStr = FileUtils::getInstance()->getStringFromFile(fullPath);

    Data datas = FileUtils::getInstance()->getDataFromFile(fullPath);

    DataType* buffer = (DataType*) datas.getBytes();
    ssize_t length = datas.getSize() / sizeof(DataType);

    for (int i = 0; i < length; i++)
    {
        Datatype dt = buffer[i];

        loadedData.push_back(dt);
    }
```

이렇게 하면 loadedData에 저장해둔 csv파일이 load 된다.
