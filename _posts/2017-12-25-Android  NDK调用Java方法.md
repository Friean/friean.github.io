---
layout: post
title: Android  NDK调用Java方法
tags: [文章]
categories: article
---


//获取hello字符串
    Camera* camera = new Camera(env);
    std::string hello = camera->SayHello();

    //------jni调用java---------
    char * className = "com/wl/jnidemo/Image";
    //获取class
    jclass dpclazz = env->FindClass(className);

    jmethodID methodID = env->GetStaticMethodID(dpclazz,"Add","(II)I");

    jint re = (*env).CallStaticIntMethod(dpclazz,methodID,2,3);

    LOG("======%d",re);
    //---------------------------

    jmethodID jmethodID1 = env->GetMethodID(dpclazz,"Daa","(II)I");

    jobject dpobj = (*env).AllocObject(dpclazz);

    jint ret = (*env).CallIntMethod(dpobj,jmethodID1,3,4);
    LOG("======%d",ret);


    //----------------------------
    jmethodID jmethodID2= env->GetMethodID(dpclazz,"arrayData","([B)[F");

    jobject dpobj2 = (*env).AllocObject(dpclazz);

    jbyteArray arr = env->NewByteArray(2);
    unsigned char* dst = new unsigned char(2);

    dst[0] = 8;
    dst[1] = 9;

    env->SetByteArrayRegion(arr,0,2,(jbyte*)dst);

    jobject array = (*env).CallObjectMethod(dpobj,jmethodID2,arr);

    jfloatArray  resutl = (jfloatArray)array;

    jfloat* params = env->GetFloatArrayElements(resutl,0);
    float *data = (float*)params;

    LOG("======%f",data[0]);
    LOG("======%f",data[1]);
    //----------------------------------