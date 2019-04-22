python之正则表达式
----
####  re模块的match，search，findall方法怎么用？


#### 

|  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |

>
>> 的关健属性：
>    head.CPOSTAG
>    词性,eg：'m'
>
>    head.DEPREL
>    包含关系,eg:'核心关系'
>
>    word.HEAD
>    父节点，与前面词语的关系
>
>    head.LEMMA
>    词语内容,eg:'20190101'
>
>    head.NAME
>    节点名称,eg:'未##数'
>
>    head.POSTAG
>    词性,eg:'m'
>


``class WordAnalys():
    
    def __init__(self):
        self.analyzer = HanLP
        CustomDictionary.add("还车", "nz")
        CustomDictionary.add("失联", "nz")
        CustomDictionary.add("失联车", "nz 1")

        
    def segment(self,sentence):
        return self.analyzer.segment(sentence)
        
    def parse(self,sentence):
        return self.analyzer.parseDependency(sentence)
        
        
    def get_words(self,sentence):
        return self._analyz(sentence)
                
    
    def _interp(self,sentence):
        output ={}# DataStruct()
        
        for x in self.segment(sentence):
            word = x.word
            nature = x.nature.toString()
            
            if nature == 'm':#数量词 
                pass
            elif nature == 't':#时间词
                pass
            elif nature.startswith('n'):#地点,名词,外文字符
                pass
            elif nature.startswith('v'):#动词
                pass
            output[word]=nature
        return output


`
