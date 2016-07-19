---
layout: post
title: 对于String的value的理解
tags:
- Java
categories: Java
description: 大家好，这次讲讲我的对java的value的理解
---
大家好，这次讲讲我的对java的value的理解

Java的String，用的地方很多，随处可见。javadoc中，String类有value的final的char数组，hash值。还有两个是关于字符串序列化的字段。所以String最主要的东西为value这个数组。

value数组的长度就是String的长度，那么对于value做一些简单的理解。

大家对于char这个类型，因该不陌生，在c,c++中，char为一个字节的长度。但是在java中，是一个字节的长度吗？答案是否定的。java的内编码是Unicode编码，Unicode分为USC-2和USC-4,java采用的是USC-4，就是32位的编码。拿java.lang.String.String(int[], int, int)函数来说，源码如下：

       public String(int[] codePoints, int offset, int count) {

        if (offset < 0) {

            throw new StringIndexOutOfBoundsException(offset);

        }

        if (count <= 0) {

            if (count < 0) {

                throw new StringIndexOutOfBoundsException(count);

            }

            if (offset <= codePoints.length) {

                this.value = "".value;

                return;

            }

        }

        // Note: offset or count might be near -1>>>1.

        if (offset > codePoints.length - count) {

            throw new StringIndexOutOfBoundsException(offset + count);

        }



        final int end = offset + count;



        // Pass 1: Compute precise size of char[]

        int n = count;

        for (int i = offset; i < end; i++) {

            int c = codePoints[i];

            if (Character.isBmpCodePoint(c))

                continue;

            else if (Character.isValidCodePoint(c))

                n++;

            else throw new IllegalArgumentException(Integer.toString(c));

        }



        // Pass 2: Allocate and fill in char[]

        final char[] v = new char[n];



        for (int i = offset, j = 0; i < end; i++, j++) {

            int c = codePoints[i];

            if (Character.isBmpCodePoint(c))

                v[j] = (char)c;

            else

                Character.toSurrogates(c, v, j++);

        }



        this.value = v;

    }

 该函数的作用是构造Unicode字符串，构造方法中，首先判断该Unicode的字符串的参数是否合法，参数满足条件后，构造value[],代码前有一个提示“        // Pass 1: Compute precise size of char[]”，就是计算value的精确的大小n，大小n的确定方法是判断当前的字符是否在BMP中，是的话则继续，不是的，看看是否在Unicode的域里没，也就是在U+0000和U+10FFFF区间。这BMP两个区间的区别是2个字节和四个字节代表一个字符。之前说的char为16位，n为value[]的大小。所以32位的Unicode需要两个char保存。在复制字符串的时候，也是同样的道理。在BMP中的Unicode只需前进一char，其余的需要前进2char。
 从这个源码分析，我们看到value存储的字符串有效的字符，字符串长度也是value的长度。对于非BMP域的Unicode的字符则长度会加倍。下面的为测试代码

    package com.t90.local;
    public class TestValues {

	public static void main(String[] args) {

		String strin = new String("虎聘网");

		byte[] bytes = strin.getBytes();

		for (int i =0;i< strin.length();i++) {

			int x = strin.codePointAt(i);

			if(Character.isBmpCodePoint(x))

				System.out.println("BMP\t"+ strin.charAt(i)+"\tunicode= "+x);

			else {

				System.out.println("NOT BMP\t"+ strin.charAt(i)+"\tunicode= "+x);

			}

		}	

		System.out.println("字符串长度\t"+strin.length()+"\n字节长度\t"+ bytes.length);

	}

    }

关于Unicode的BMP，请查阅相关资料，感谢 谈谈Unicode编码，简要解释UCS、UTF、BMP、BOM等名词，提供相关资料。
