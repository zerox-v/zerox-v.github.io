---
title: .Net点选验证码实现思路分享
date: 2023-06-18 11:26:50
tags: 
- 验证码
- 点选验证码
categories:
- 验证码
---
哈哈好久没冒泡了，最进看见点选验证码有点意思，所以想自己写一个。
先上效果图
![2019-05-22-14-55-15](//qiniu.ionic.fun/689553-20170107154417675-959851559.gif)
如果你被这个效果吸引了就请继续看下去。

贴代码前先说点思路：

1.要有一个汉字库,并按字形分类。（我在数据库里是安部首分类的）

2.获取验证码（也就是取几个文字做验证码）

3.根据取出来的文字去找形近字

4.排列验证码文字和形近字

5.绘制图片

6.显示

6.写个博客分享一下（分享代码改变世界）

 

## 获取字库

　　我国文化博大精深，辣么多的字从哪儿来？当然我不可能手动加进去，于是我就在网上随便找了一个能查汉字的网站，去抓别人的数据。抓数据的方法请点传送门。传送门里说的只是思路，如果有不明白的请艾特我。我会在下面共享我的字库。

 

## 获取验证码

   这个就比较简单了这里我就直接贴代码了，下面的代码就是随机排序后取4条数据，我这样写是为了图方便。个人觉得先随机生成ID，然后直接根据ID取数据，这样查询速度会比下面这种写法快。（注意我用的数据库是MySql）

``` C#
        /// <summary>
        /// 获取验证码
        /// </summary>
        public List<VerificationCode.Model.WenZhi> GetCodeText()
        {
            const string sql = "SELECT * FROM wenzhi ORDER BY RAND() LIMIT 4";
            var dataReader = dbHelper.GetDataReader(sql);
            var list = DataReaderToList(dataReader);
            dataReader.Close();
            return list;
        }
```
 

### 根据取出来的文字去找形近字

　　因为第一步的时候我存部首了，所以这里我直接根据部首取获取当前部首的形近字。

``` C#
        /// <summary>
        /// 获取答案备选
        /// </summary>
        /// <param name="buShouCode">部首编码</param>
        /// <param name="id">当前文字ID</param>
        /// <param name="number">数量</param>
        /// <returns></returns>
        public List<VerificationCode.Model.WenZhi> GetAnswer(string buShouCode, int id,int number=1)
        {
            string sql = $"SELECT * FROM wenzhi where BuShouCode='{buShouCode}' and ID <> {id} ORDER BY RAND() LIMIT "+ number;
            var dataReader = dbHelper.GetDataReader(sql);
            var list = DataReaderToList(dataReader);
            dataReader.Close();
            return list;
        }
```
 

## 排列验证码文字和形近字

　　下面的代码是先把备选答案和验证码放在一个集合里然后再对集合排序

``` C#
 public Model.Code GetCode()
        {
          
            var wenzlist = _wenZhiDal.GetCodeText();  //获取验证码
            var listAnsuwr = new List<Answer>();//实例化备选答案对象
            var answerCode = string.Empty;//答案
            var result = new Model.Code
            {
                Id = Guid.NewGuid().ToString()
            };
            //根据验证码获取备选答案并把添加到答案添加到备选答案集合
            foreach (var item in wenzlist)
            {
                answerCode += item.ID + ",";
                result.AnswerValue += item.Text;
                var answerList = _wenZhiDal.GetAnswer(item.BuShouCode, item.ID);
                listAnsuwr.Add(new Answer { Id = item.ID.ToString(), Img = GetImage(item.Text) });
                listAnsuwr.AddRange(answerList.Select(answer => new Answer { Id = answer.ID.ToString(), Img = GetImage(answer.Text) }));
            }
            //如果答案个数不够就再去取几个
            if (listAnsuwr.Count < 9)
            {
                var ran = new Random();
                var randKey = ran.Next(0, 4);
                var item = wenzlist[randKey];
                var answerList = _wenZhiDal.GetAnswer(item.BuShouCode, item.ID, 9 - listAnsuwr.Count);
                listAnsuwr.AddRange(answerList.Select(answer => new Answer { Id = answer.ID.ToString(), Img = GetImage(answer.Text) }));
            }
            result.CodeImg = GetImage(result.AnswerValue);//获取图片
            result.AnswerValue = answerCode.TrimEnd(',');
            result.Answer = RandomSortList(listAnsuwr);//打乱正确答案与形近字的顺序
            return result;
        }
```
 这是对集合排序的代码

```C#
        /// <summary>
        /// 随机排列集合
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="listT"></param>
        /// <returns></returns>
        private static List<T> RandomSortList<T>(IEnumerable<T> listT)
        {
            var random = new Random();
            var newList = new List<T>();
            foreach (var item in listT)
            {
                newList.Insert(random.Next(newList.Count + 1), item);
            }
            return newList;
        }
```
 

## 绘制图片

 下面是画图的代码，验证码和备选答案对应两种不同的画法（里面注释写的还算清楚）。不要担心文字旋转x°后人类分不出来，哈哈。代码最后一句我把图片转成了Base64，方便前端调用。

 
```C#
private static string GetImage(string text)
        {
            Image image;
            switch (text.Length)
            {
                case 1:
                    image = new Bitmap(50, 40);
                    break;
                case 4:
                    image = new Bitmap(120, 40);
                    break;
                default:
                    image = new Bitmap(50, 40);
                    break;
            }
            Brush brushText = new SolidBrush(Color.FromArgb(255, 0, 0, 0));
            var graphics = Graphics.FromImage(image);
            graphics.SmoothingMode = SmoothingMode.AntiAlias;
            graphics.Clear(Color.White);
            var font = new Font(new FontFamily("华文彩云"), 20, FontStyle.Regular);
            if (text.Length > 1)//画验证码
            {
                //先来两条直线做干扰 然后再画文字
                graphics.DrawLine(new Pen(brushText, new Random().Next(1, 3)), new Point(new Random().Next(0, 10), new Random().Next(10, 40)), new Point(new Random().Next(100, 120), new Random().Next(10, 30)));
                graphics.DrawLine(new Pen(brushText, new Random().Next(1, 3)), new Point(new Random().Next(20, 50), new Random().Next(0, 10)), new Point(new Random().Next(100, 120), new Random().Next(30, 40)));
                graphics.DrawString(text, font, brushText, 0, 10);

            }
            else//画备选答案
            {
                Point middle = new Point(25, 20);
                graphics.TranslateTransform(middle.X, middle.Y);
                //这里是360°随机旋转 
                graphics.RotateTransform(new Random().Next(0, 360));
                var format = new StringFormat(StringFormatFlags.NoClip)
                {
                    Alignment = StringAlignment.Center,
                    LineAlignment = StringAlignment.Center
                };
                graphics.DrawString(text, font, brushText, 0, 0, format);

            }
            brushText.Dispose();
            graphics.Dispose();
            return ImageToBase64(image);
        }
```
 

## 显示

直接调用GetCode方法就能返回验证码对象

下面是后台代码,应为正确答案是放在AnswerValue里的所以我先把取出来放Session里面，然后把值清空后再通过json返回给浏览器。

``` C#
        public string GetVerCode()
        {
            var code = new VerificationCode.Code().GetCode();
            Session["VERCODE"] = code.AnswerValue;
            code.AnswerValue = "";
            return JsonConvert.SerializeObject(code);
        }
```
现在来堆点html代码

```html
<div class="form-group">
                    <ul class="vercode">
                        <li><img src=''/></li>
                        <li><img src=''/></li>
                        <li><img src=''/></li>
                        <li><img src=''/></li>
                        <li class="delete" onclick="delete_input()"></li>
                    </ul>
                    <div>
                        <img id="code-image"/> <a href="javascript:void(0);" onclick="load_vercode()">看不清？</a>
                    </div>
                    <ul class="vercode-anwser">
                        <li><img /></li>
                        <li><img /></li>
                        <li><img /></li>
                        <li><img /></li>
                        <li><img /></li>
                        <li><img /></li>
                        <li><img /></li>
                        <li><img /></li>
                        <li><img /></li>
                    </ul>
                </div>
```
 

再来点js代码,这里只实现的图片上的效果，还没对数据验证（这里说说验证思路：每个图片对应一个ID，取到选择图片的ID用逗号分隔，然后与Session里的值对比）

```javascript
<script>
        $(function () {
            //加载验证码
            load_vercode();
            //绑定验证码点击事件
            $(".vercode-anwser").find("img").on("click", null, function () {
                $(".vercode").find("img[src='']:eq(0)").attr("src", $(this).attr("src"));
            });
        });

        function load_vercode() {
            $(".vercode").find("img").attr("src", "");
            $.get("GetVerCode", function (data) {
                var result = JSON.parse(data);
                $("#code-image").attr("src", "data:image/png;base64," + result.CodeImg);
                $(".vercode-anwser").find("img").each(function (index) {
                    $(this).attr("src", "data:image/png;base64," + result.Answer[index].Img);
                });
            });
        }
        //删除事件
        function delete_input() {
            $(".vercode").find("img[src!='']:last").attr("src", "");
        }
    </script>
```
到这里代码就差不多了,以上思路只是单纯的个人想法，有兴趣的朋友一起来讨论吧。

源代码在这里-->地址 http://pan.baidu.com/s/1eS5Mn30 密码：7iud

分享代码改变世界