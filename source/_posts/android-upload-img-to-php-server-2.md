---
title: Android拍照上传至PHP服务器并写入MySql数据库(下)
date: 2016-07-22 15:45:32
tags:
 - PHP
 - Android
---

# Android实现 #

调用系统相机，拍照：

     Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
                getFileUri();
                intent.putExtra(MediaStore.EXTRA_OUTPUT, file_uri);
                startActivityForResult(intent, CODE_CAMERA);


      private void getFileUri() {
        image_name = Calendar.getInstance().getTimeInMillis() + ".jpg";
        file = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES) + File.separator + image_name);
        file_uri = Uri.fromFile(file);
    }

<!--more-->

在onActivityResult里面接收图片并Base64处理：

      @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {

        if (requestCode == CODE_CAMERA && resultCode == RESULT_OK) {
            new EncodeImage().execute();   //把bitmap转换成base64字符串
        }
    }

EncodeImage是一个AsyncTask，doInBackground里面从uri里面获取bitmap，然后转入输出流，最终转换为base64编码字符串：

      @Override
        protected Void doInBackground(Void... voids) {
            bitmap = BitmapFactory.decodeFile(file_uri.getPath());
            ByteArrayOutputStream stream = new ByteArrayOutputStream();
            bitmap.compress(Bitmap.CompressFormat.JPEG, 80, stream);
            byte[] array = stream.toByteArray();
            encoded_string = Base64.encodeToString(array, 0);
            bitmap.recycle();  //防止oom
            return null;
        }

然后就可以上传到服务器了：

        private void uploadImage() {
        HashMap<String, String> map = new HashMap<>();
        map.put("encoding_string", encoded_string);
        map.put("image_name", image_name);
        OkHttpUtils.post()
                .url("http:192.168.0.112/phpdemo/uploadimage.php")
                .params(map)
                .tag(this)
                .build()
                .execute(new StringCallback() {
                    @Override
                    public void onError(Call call, Exception e, int id) {
                        Log.e("出错了", "错误信息：" + e.getMessage());
                    }

                    @Override
                    public void onResponse(String response, int id) {
                        Log.e("成功or失败", "信息：" + response);
                    }
                });
    }


在上传服务器过程中，遇到两个问题，第一,提示`POST Content-Length of  ... bytes exceeds the limit of 8388608 bytes`,这个错误是因为php默认最大post上传8M,更改php.ini里面的`post_max_size=1000M`就ok了；第二，当第二次拍照的时候会出现OOM的情况，检查代码发现bitmap没有recycle。


OVER