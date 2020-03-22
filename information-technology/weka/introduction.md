# Weka

Weka is a collection of machine learning algorithms for data mining tasks. The algorithms can either be applied directly to a dataset or called from your own Java code. 

Weka contains tools for **data pre-processing, classification, regression, clustering, association rules, and visualization.** It is also well-suited for developing new machine learning schemes.

# Datasets

Each entry in a dataset is an instance of the java class
That is, weka.core.Instance

Each weka.core.instance consists of a number of attributes

## Attributes

Nominal
: one of a predefined list of values 
− e.g. red, green, blue 

Numeric
: A real or integer number

String
: Enclosed in “double quotes”

Date
: date

# ARFF 

The external representation of an Instances class.
ARFF file is consists of Header & Data section.

Header
: Describes the attribute types

Data section
: Comma separated list of data

```
//HEADER
@relation weather
@attribute outlook {sunny, cloudy, rainy} // Nominal
@attribute temperature real // Numeric
@attribute windy {TRUE, FALSE} // Nominal
@attribute play {yes, no} //target, class variable

//DATA SECTION
@data
sunny,10,TRUE,yes
cloudy,22,FALSE,yes
...
```

# Classifiers in Weka

Learning algorithms in Weka are derived from the abstract class. That is weka.classifiers.Classifier.

ZeroR(Simple classifier)
: Just determines the most common class or The median (in the case of numeric values). Tests how well the class can be predicted without considering other attributes. ZeroR can be used as a Lower Bound on Performance.

## Test Results

arff can be splited into train and test set
- *-train.arff
- *-test.arff

Input command
```
java weka.classifiers.trees.J48 -t soybean-train.arff -T soybean-test.arff -i
```

True Positive Rate(TP) == Recall
: 테스트 데이터의 클래스 x에 해당하는 데이터 엔트리 중에서 클래스 x로 올바르게 분류한 비율

False Positive Rate(FP)
: 테스트 데이터의 클래스 x에 해당하는 데이터 엔트리 중에서 클래스 x가 아닌 것으로 잘못 분류한 비율

Precision
: 클래스 x로 분류된 데이터 중에서 테스트 데이터가 실제 클래스 x인 비율

F-measure
: 2 * Precision * Recall / (Precision + Recall)
i.e. A combined measure for precision and recall

참고 문헌

[https://www.cs.auckland.ac.nz/courses/compsci367s1c/tutorials/IntroductionToWeka.pdf](https://www.cs.auckland.ac.nz/courses/compsci367s1c/tutorials/IntroductionToWeka.pdf)

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA4MzM5OTc5MSwtMTE5Mjk4NjcxNywtMj
EzMDU5MjYzNiwtMTA0NjgxMTg4MSwtMTA5NjgzNTAxMywxMzQw
NjIwMjY4XX0=
-->