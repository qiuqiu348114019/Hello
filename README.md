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
 public function safeUp()
    {
        // 表设置
        $options = '';
        if ($this->db->driverName === 'mysql') {
            $options = 'CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci ENGINE=InnoDB';
        }

        // 创建表
        $this->createTable('{{%goods}}', [
            'id' => $this->bigInteger()->notNull()->comment('商品编号'),
            'store_id' => $this->bigInteger()->notNull()->comment('店铺编号'),
            'name' => $this->string(150)->notNull()->comment('商品名称'),
            'current_price_min' => $this->decimal(10,2)->notNull()->comment('当前价：最低区间'),
            'current_price_max' => $this->decimal(10,2)->notNull()->comment('当前价：最高区间'),
            'one_price_min' => $this->decimal(10,2)->notNull()->comment('单购价：最低区间'),
            'one_price_max' => $this->decimal(10,2)->notNull()->comment('单购价：最高区间'),
            'state' => $this->string(10)->notNull()->defaultValue('UP')->comment('状态[UP:在售][DOWN:下架]'),
            'evaluate' => $this->integer()->notNull()->comment('评价数'),
            'stock' => $this->integer()->notNull()->comment('剩余库存'),
            'sales_numbers' => $this->integer()->notNull()->comment('总销量'),
            'visitors' => $this->bigInteger()->defaultValue(0)->comment('总访客数'),
            'views' => $this->bigInteger()->defaultValue(0)->comment('总浏览量'),
            'paid_orders' => $this->integer()->defaultValue(0)->comment('总支付订单数'),
            'cvr' => $this->float(4,2)->defaultValue(0.00)->comment('平均支付转化率（%）'),
            'collection' => $this->integer()->defaultValue(0)->comment('总收藏量'),
            'created_at'=>$this->dateTime()->comment('创建时间'),
            'updated_at'=>$this->dateTime()->comment('更新时间'),
            'version'=>$this->bigInteger()->defaultValue(1)->comment('乐观锁'),
            'PRIMARY KEY(id)',
        ], $options);


        // 表注释
        $this->addCommentOnTable('{{%goods}}', '商品表');

        // 店铺表外键
        $this->addForeignKey(
            'fk_goods_store_id',
            'goods',
            'store_id',
            'stores',
            'id',
            'CASCADE'
        );

    }
