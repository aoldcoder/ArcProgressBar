# ArcProgressBar
RxJava返回responsebody 解决接口返回数据结构不一致问题

 @Override
    public void login(String email, String password) {

        apiServer.newLogin2(email, password)
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .map(new Func1<ResponseBody, String>() {
                    @Override
                    public String call(ResponseBody responseBody) {
                        String data = "";
                        try {
                            data = responseBody.string();
                            LogUtil.i(TAG, data);
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                        return data;
                    }
                }).subscribe(new SubscriberCallBack(new ApiCallback<String>() {
            @Override
            public void onSuccess(String model) {
                try {
                    JSONObject jsonObject = new JSONObject(model);
                    if (jsonObject.has("status")) {
                        String status = jsonObject.getString("status");
                        String msg = jsonObject.getString("msg");
                        String data = jsonObject.getString("data");

                        if (status.equals("200")) {
                            //保成登录信息
                          LoginBean bean =   saveLoginInfo(data);
                            mView.loginSuccess(bean);
                        }else{
                            mView.loginFailed(msg);
                        }
                    }
                    LogUtil.i(TAG, model.toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            public void onFailure(String msg) {
                LogUtil.i(TAG,msg);
            }
        }));
