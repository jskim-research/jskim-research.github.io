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

앞으로의 글에서는 코드만 올릴 예정이지만 필자 개인적으로 프레임워크를 발전시켜나가면서 로직에 집중하고자 한다.

아직 개선의 여지가 많은 프레임워크[[1]]이기 때문에 기여하고 싶으시다면 언제든지 댓글 바랍니다.

## 입력

ProblemFile.h 헤더파일에는 입력을 담당하는 함수들이 존재한다.

최상위 클래스인 ProblemFile을 상속받아 ReadFile 함수를 수정함으로써 다양한 문제 풀이 사이트의 입력에 대응할 수 있다.

아래 코드는 프로그래머스 입력 파일에 대응하는 ReadFile 함수의 예시다.

```cpp
// ProblemFile.h
inline void ProgrammersFile::ReadFile(std::string file_name, std::vector<FileFormat::Types> type)
{
	std::fstream ifs;
	std::string buffer;

	ifs.open(file_name);

	if (ifs.is_open())
	{
		while (ifs.peek() != EOF)
		{
			std::getline(ifs, buffer);

			// 입력이 개행 문자로 나뉜다는 가정
			this->num_of_problems++;

			std::vector<std::string> split_string = StringOperation::split(buffer, '\t');
			for (int i = 0; i < split_string.size(); i++)
			{
 				std::string s = split_string[i];
				std::vector<std::string> array;

				if (type[i] == FileFormat::Types::List)
				{
					s = StringOperation::remove_all(StringOperation::remove_all(StringOperation::trim(split_string[i]), ' '), '\"');
				}
				
				array = StringOperation::split(s, ',');
				arrays.push_back(array);
			}
		}
	}
	else
	{
		throw ifs;
	}

	ifs.close();
}
```

## 로직

Problem class의 Run 함수엔 크게 Prepare (입력), Solve (로직), Score (출력) 함수가 쓰인다.

여기서 Problem class를 상속한 자식 클래스에서 Solve 함수를 구현하는 형태이며 디자인 패턴을 안다면 template method 라고 보면 된다.

```cpp
// Problem.cpp
void Problem::Run()
{
	vector<vector<string>> inputs;
	vector<string> output;
	vector<string> right_answer;
	int num_problems = 0;

	Prepare(); // 입력!
	inputs = file->GetArrays();
	num_problems = file->GetNumOfProblems();

	for (int test_case = 0; test_case < inputs.size(); test_case += inputs.size() / num_problems)
	{
		vector<vector<string>> input;
		for (int i = test_case; i < test_case + inputs.size() / num_problems; i++)
		{
			input.push_back(inputs[i]);
		}
		right_answer = inputs[test_case + inputs.size() / num_problems - 1];
		output = Solve(input); // 로직!
		Score(output, right_answer); // 출력!
	}

}
```

# 활용법

실제 문제를 풀 때는 각 문제 사이트에 맞는 Problem class를 상속하고 Solve 함수만 구현하면 된다.

밑 소스코드는 2020 카카오 인턴십 문제인 보석 쇼핑을 풀 때 사용한 것이다.

```cpp
// JewelShopping.h

class JewelShopping : public ProgrammersProblem
{
public:
	JewelShopping(FileFormat file_format);
	std::vector<std::string> Solve(std::vector<std::vector<std::string>>& input) override;
	int GetCatNum(const std::vector<std::string>& gems);
};
```

![Figure 1](/assets/images/CodingTestFramework8.png "Figure 1"){: width="100%" height="100%" .align-center}
*Figure 1. 실행 결과*

Figure 1과 같이 main.cpp에서 Solve 함수를 호출하면 결과를 확인할 수 있다.

# 결론

프레임워크를 [[1]] 에서 git pull을 통해 다운로드 받은 후에 visual studio로 바로 결과를 확인할 수 있으니 기존 문제 풀이 사이트만 이용하는 것보다 편리할 수 있을 것이다.

다만 일반적인 프레임워크를 만든다는 것이 쉽지는 않은 일이며 무엇보다 많은 사람이 사용할 지 의문이 있다.

이러한 프레임워크 설계에 관심이 있다면 연락 주길 바란다. 같이 한번 발전시켜볼 수 있을 것 같다.

[1]: https://github.com/jskim-research/CodingTest

