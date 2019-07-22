---
title: java-ArrayList根据某个属性去重
date: 2019-07-01 09:45:26
tags:
- java
categories:
- java
---

### 实体类
```java
public class Student
{
	private String sno;

	private String name;

	private int sex;

	public Student(String sno, String name, int sex)
	{
		this.sno = sno;
		this.name = name;
		this.sex = sex;
	}

	public String getName()
	{
		return name;
	}

	public void setName(String name)
	{
		this.name = name;
	}

	public String getSno()
	{
		return sno;
	}

	public void setSno(String sno)
	{
		this.sno = sno;
	}

	public int getSex()
	{
		return sex;
	}

	public void setSex(int sex)
	{
		this.sex = sex;
	}

	@Override
	public String toString()
	{
		return "Student [name=" + name + ", sno=" + sno + ", sex=" + sex + "]";
	}

}
```

### 根据sno字段去重
```java
List<Student> list = new ArrayList<Student>();

for (int i = 0; i < 3; i++)
{
  list.add(new Student("123", "name", i));
}

list = list.stream().collect(
    Collectors.collectingAndThen(
        Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Student::getSno))),
        ArrayList::new));
```
