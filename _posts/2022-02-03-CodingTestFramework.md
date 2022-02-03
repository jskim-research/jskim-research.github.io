---
layout: single
title: "문제풀이 프레임워크"

toc: true
toc_label: INDEX
toc_sticky: false

categories:
  - CodingTest
---

# Introduction

코딩 테스트 문제는 다음 세 가지로 구성된다.

"입력, 로직, 출력"

입력, 로직, 출력을 class로 추상화함으로써 기존의 지겨운 입출력 구현을 없애고 로직 구현에 집중할 수 있도록 한다.

또한 본 블로그의 문제풀이 소스코드를 프레임워크에서 바로 실행해보면서 테스트할 수 있게 하고자 한다.

# Class 설계

![Figure 1](/assets/images/CodingTestFramework1.png "Figure 1"){: width="50%" height="50%" .align-center}
*Figure 1. Header files*

/Problem.h : 로직을 담당하는 Solve 함수 선언  
/ProblemFile.h : 입출력 파일을 읽어들이는 기능 및 변수 선언  오후 5:08 2022-02-03
/Layers/*.h : 공통적으로 쓰이는 header 파일들 집합  
/StringOperation.h : 자주 쓰는 문자열 함수 선언  
/SolvedProblems/*.h : 문제를 class로 선언  

여기선 로직을 담당하는 Problem class와 입출력 파일을 읽어들이는 ProblemFile class를 집중적으로 설명한다.

## ProblemFile.h

```cpp
class ProblemFile
{
protected:
	size_t num_of_problems;
	std::vector<std::vector<std::string>> arrays;
public:
	enum Types { List, Value };
	ProblemFile();
	virtual ~ProblemFile();
	size_t GetNumOfProblems() const;
	std::vector<std::vector<std::string>> GetArrays() const;
	virtual void ReadFile(std::string file_name, std::vector<Types> type) {}
};

class ProgrammersFile : public ProblemFile
{
private:

public:
	ProgrammersFile();
	~ProgrammersFile();
	void ReadFile(std::string file_name, std::vector<Types> type) override;
};
```

ProblemFile 클래스의 핵심은 문제의 입출력 파일을 읽는 ReadFile 함수이다. 

자식 클래스에서 ReadFile을 override 함으로써 문제 사이트마다 다른 입출력 파일 양식에 대응할 수 있다.

## Problem.h

```cpp
class Problem
{
protected:
	ProblemFile *file;
public:
	Problem();
	virtual ~Problem() = 0;
	virtual void Solve() = 0;
};

class ProgrammersProblem : public Problem
{
public:
	ProgrammersProblem();
};
```

Problem class에선 로직을 구현하는 Solve 함수가 핵심이다.

로직을 구현하기 위해선 문제에서 정의된 입출력 파일이 필요하므로 ProblemFile 변수를 가지고 있다.

# 실제 문제 정의

실제 문제를 풀 때는 각 문제 사이트에 맞는 Problem class를 상속하고 Solve 함수만 구현하면 된다.

밑 소스코드는 20202 카카오 인턴십 문제인 보석 쇼핑을 풀 때 사용한 것이다.

입출력을 담당하는 부분을 최소화 하여 로직 구현에 집중할 수 있다.

```cpp
/*
2020 카카오 인턴십 > 보석 쇼핑
https://programmers.co.kr/learn/courses/30/lessons/67258
*/

class JewelShopping : ProgrammersProblem
{
public:
	void Solve() override;
	int GetCatNum(const std::vector<std::string>& gems);
};
```

```cpp
void JewelShopping::Solve()
{
	cout << "Problem [JewelShopping]" << endl;
	file->ReadFile("./JewelShopping.txt", { ProblemFile::Types::List, ProblemFile::Types::List });

	vector<vector<string>> arrays = file->GetArrays();
	int num_problems = file->GetNumOfProblems();

	for (int test_case = 0; test_case < arrays.size(); test_case += arrays.size() / num_problems)
	{
		...
	}
}
```

```
// ./JewelShopping.txt
["DIA", "RUBY", "RUBY", "DIA", "DIA", "EMERALD", "SAPPHIRE", "DIA"]	[3, 7]
["AA", "AB", "AC", "AA", "AC"]	[1, 3]
["XYZ", "XYZ", "XYZ"]	[1, 1]
["ZZZ", "YYY", "NNNN", "YYY", "BBB"]	[1, 5]
["AA"]	[1, 1]
```

![Figure 1](/assets/images/CodingTestFramework8.png "Figure 1"){: width="100%" height="100%" .align-center}
*Figure 1. 실행 결과*

Figure 1과 같이 main.cpp에서 Solve 함수를 호출하면 결과를 확인할 수 있다.

# 결론

앞으로 위 프레임워크를 기반으로 문제풀이 코드를 작성해나갈 생각이다.

프레임워크를 [[1]] 에서 git pull을 통해 다운로드 받은 후에 visual studio로 바로 결과를 확인할 수 있으니 독자들의 이해에 도움이 되리라 생각한다.

다만 입출력과 로직이 완벽하게 분리가 되지 않아 향후 개선할 예정이다.

[1]: https://github.com/jskim-research/CodingTest


