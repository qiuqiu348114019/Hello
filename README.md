# Hello
创造你好
创造新的主人学习指南的方法
 /**
     * 店铺数据推送
     */
    public function actionPush()
    {

        $db = Yii::$app->db;
        $transaction = $db->beginTransaction();
        try{
            //获取请求数据，同时提交表单验证
            $post = Yii::$app->request->post();
            $model = Stores::findOne($post['id']);
            if (empty($model)){
                $model = new Stores();
            }
            //发送验证
            $model->setAttributes($post);
            if ($model->validate() && $model->save()) {
                $transaction->commit();
                return $this->successResponse();
            } else {
                $transaction->rollBack();
                $e = $model->errors;

            }
        }
        catch (\Exception $e){
            $transaction->rollBack();

            }
        return $this->failResponse(400,$e);


    }
