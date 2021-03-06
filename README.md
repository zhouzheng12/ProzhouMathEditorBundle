# ProzhouMathEditorBundle
   在jims/editor-hub-bundle基础上添加公式编辑器功能，支持多个实例
## 安装 
  1.Step 1 composer 安装
  ```
  composer require jims/editor-hub-bundle
  composer require prozhou/math-editor-bundle
  
  ```
  
  
  ```
  2.Step 2 添加到appKernel.php  同时 更新资源
  ```php
  // app/AppKernel.php

  public function registerBundles()
  {
      $bundles = array(
          // ...
          new Jims\EditorHubBundle\JimsEditorHubBundle(),
          new Prozhou\MathEditorBundle\ProzhouMathEditorBundle(),
      );
  }
  
  //then update assets 更新资源:
  
  app/console assets:install web --symlink   #sf2
  bin/console assets:install web --symlink   #sf3
  ```
  3.Step 3 Import routes to app/config/routing.yml
  ```yml
  // app/config/routing.yml
  jims_editor:
    resource: "@JimsEditorHubBundle/Resources/config/routing.yml"
    prefix:   /
  ```
  4.Step 4 Import form themes to app/config/config.yml
  ```yml
  //app/config/config.yml
  twig:
    ...
    form_themes:
        - 'JimsEditorHubBundle:Form:form_ueditor_type.html.twig'
        - 'JimsEditorHubBundle:Form:form_umeditor_type.html.twig'
  ```
  5.Step 5 添加设置bundle 配置 于 app/config/config.yml
  ```yml
  //app/config/config.yml
  jims_editor_hub:
    ueditor:
        config_file: ~       #ueditor 的 配置文件的 默认文件： "bundle路径"+Resources/config/config.json
    umeditor:
        save_path:    "upload/umeditor/"                                  #存储文件夹
        max_size:     2000                                                #允许的文件最大尺寸，单位KB
        allow_files:  [ ".gif" , ".png" , ".jpg" , ".jpeg" , ".bmp" ]     #允许的文件格式
  ```
## 使用方法
 
 ```php
<?php

namespace AppBundle\Controller;

use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Symfony\Component\HttpFoundation\Request;
use AppBundle\Entity\Task;
use Symfony\Component\Form\Extension\Core\Type\SubmitType;
use Jims\EditorHubBundle\Form\UeditorType;


class DefaultController extends Controller
{


    /**
     * @Route("/editor")
     */
    public function newAction(Request $request)
    {

        $toolbars = [
            'anchor', //锚点
            'undo', //撤销
            'redo', //重做
            'bold', //加粗
            'indent', //首行缩进
            'snapscreen', //截图
            'italic', //斜体
            'underline', //下划线
            'strikethrough', //删除线
            'subscript', //下标
            'fontborder', //字符边框
            'superscript', //上标
            'formatmatch', //格式刷
            'source', //源代码
            'blockquote', //引用
            'pasteplain', //纯文本粘贴模式
            'selectall', //全选
            'print', //打印
            'preview', //预览
            'horizontal', //分隔线
            'removeformat', //清除格式
            'time', //时间
            'date', //日期
            'unlink', //取消链接
            'insertrow', //前插入行
            'insertcol', //前插入列
            'mergeright', //右合并单元格
            'mergedown', //下合并单元格
            'deleterow', //删除行
            'deletecol', //删除列
            'splittorows', //拆分成行
            'splittocols', //拆分成列
            'splittocells', //完全拆分单元格
            'deletecaption', //删除表格标题
            'inserttitle', //插入标题
            'mergecells', //合并多个单元格
            'deletetable', //删除表格
            'cleardoc', //清空文档
            'insertparagraphbeforetable', //"表格前插入行"
            'insertcode', //代码语言
            'fontfamily', //字体
            'fontsize', //字号
            'paragraph', //段落格式
            'simpleupload', //单图上传
            'insertimage', //多图上传
            'edittable', //表格属性
            'edittd', //单元格属性
            'link', //超链接
            'emotion', //表情
            'spechars', //特殊字符
            'searchreplace', //查询替换
            'map', //Baidu地图
            'gmap', //Google地图
            'insertvideo', //视频
            'help', //帮助
            'justifyleft', //居左对齐
            'justifyright', //居右对齐
            'justifycenter', //居中对齐
            'justifyjustify', //两端对齐
            'forecolor', //字体颜色
            'backcolor', //背景色
            'insertorderedlist', //有序列表
            'insertunorderedlist', //无序列表
            'fullscreen', //全屏
            'directionalityltr', //从左向右输入
            'directionalityrtl', //从右向左输入
            'rowspacingtop', //段前距
            'rowspacingbottom', //段后距
            'pagebreak', //分页
            'insertframe', //插入Iframe
            'imagenone', //默认
            'imageleft', //左浮动
            'imageright', //右浮动
            'attachment', //附件
            'imagecenter', //居中
            'wordimage', //图片转存
            'lineheight', //行间距
            'edittip ', //编辑提示
            'customstyle', //自定义标题
            'autotypeset', //自动排版
            'webapp', //百度应用
            'touppercase', //字母大写
            'tolowercase', //字母小写
            'background', //背景
            'template', //模板
            'scrawl', //涂鸦
            'music', //音乐
            'inserttable', //插入表格
            'drafts', // 从草稿箱加载
            'charts', // 图表
        ];

        $toolbarString = "[[ '" . implode("' , '", $toolbars ) . "' ]]";
        // create a task and give it some dummy data for this example
        $task = new Task();
        $task->setTask('Write a blog post');
        $task->setDueDate('Write a blog post');

        $form = $this->createFormBuilder($task)
            ->add('task', UeditorType::class, array(
                "attr" => array(
                    "style" => "height:400px;", //editor转换成编辑器编辑空间尺寸
                    "class"=>"jims",
                ),
                //通过自定义js, 控制editor toolbars
                'js_script' => "window.UEDITOR_CONFIG.toolbars=".$toolbarString,
            ))

            ->add('dueDate', UeditorType::class, array(
                "attr" => array(
                    "style" => "height:400px;", //editor转换成编辑器编辑空间尺寸
                    "class"=>"jims",
                ),
                //通过自定义js, 控制editor toolbars
                'js_script' => "window.UEDITOR_CONFIG.toolbars=".$toolbarString,
            ))
            ->add('save', SubmitType::class, array('label' => 'Create Task'))
            ->getForm();

        return $this->render('default/new.html.twig', array(
            'form' => $form->createView(),
        ));
    }
}

   ```
![editor](editor.png)
