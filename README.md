# 3118005364
论文查重算法实验
```python
import jieba #jieba库，用于中文分词
import math 
import re 

#进行读取文本文件工作
def read_text(path):
    str = ''
    file = open(path, 'r', encoding='UTF-8') #打开文件，以 UTF-8 编码方式处理
    line = file.readline()
    while line:
        str += line
        line = file.readline()
    #file.readline()读取文件的一行，然后将“指针”移动到\n的后面
    file.close()
    return str

 #进行文本清洗和分词工作 (文本清洗：去除标点、统一大小写等)
def text_clean(str):  
    pattern = re.compile(u"[^a-zA-Z0-9\u4e00-\u9fa5]")   	
    str = pattern.sub("",str)
	#文本清洗。定义正则表达式匹配模式，其中^匹配字符串的开头,保留a-z、A-Z、0-9和汉字
    new = []
    new = [i for i in jieba.cut(str, cut_all=False) if i != '']  #进行分词。cut_all=False精确
    return new

#利用余弦公式来计算文本相似度
def cos(new1,new2):
	word_set = set(new1).union(set(new2))  
	word_dict = dict()
	i = 0
	for word in word_set:
		word_dict[word] = i
		i += 1
	text1_cut_index = [word_dict[word] for word in new1]
	text2_cut_index = [word_dict[word] for word in new2]
	text1_cut_index = [0]*len(word_dict) 
	text2_cut_index = [0]*len(word_dict)
	for word in new1:
		text1_cut_index[word_dict[word]] += 1
	for word in new2:
		text2_cut_index[word_dict[word]] += 1
	sum = 0 
	sq1 = 0
	sq2 = 0
	for i in range(len(text1_cut_index)):
		sum += text1_cut_index[i] * text2_cut_index[i]
		sq1 += pow(text1_cut_index[i], 2)
		sq2 += pow(text2_cut_index[i], 2)
	
	try:
		cos_result = round(float(sum) / (math.sqrt(sq1) * math.sqrt(sq2)), 2)
	except ZeroDivisionError:
		cos_result = 0.0
	return cos_result

def main():
    path1 = r'C:/Users/软件工程/test/test/orig.txt'  #原文
    path2 = r'C:/Users/软件工程/test/test/orig_0.8_dis_15.txt'  #被查重内容
    keep_path = r'C:/Users/软件工程/test/test/keep.txt'   #输出
    str1 = read_text(path1)
    str2 = read_text(path2)
    text1 = text_clean(str1)
    text2 = text_clean(str2)
    cos_result = cos(text1,text2)
    print("文章的相似度为： %.4f"%cos_result)
    #将结果记录在keep.txt文件里
    file = open(keep_path, 'w', encoding="utf-8") 
    file.write("文章的相似度为： %.4f"%cos_result)
    file.close()

if __name__ == '__main__':
	main ()
```
