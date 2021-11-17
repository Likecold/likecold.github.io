---
layout:     post
title:      Android Listview复杂列表的处理
subtitle:   listview优化
date:       2019-06-05
author:     ZY
header-img: img/bg-android.jpeg
catalog: true
tags:
    - Android
---

## Android Listview复杂列表的处理

### 概念
电商类app, 订单信息较复杂，页面复杂度较高，可利用组件复用机制进行优化处理。
### listview的使用

- 布局 （xml）

  ```xml
  <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      xmlns:tools="http://schemas.android.com/tools"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:orientation="vertical"
      tools:context=".MainActivity">
      <ListView
          android:id="@+id/my_list"
          android:layout_width="match_parent"
          android:layout_height="match_parent">
      </ListView>
  </androidx.constraintlayout.widget.ConstraintLayout>
  ```

  

- 数据（Adapter）

  ```java
  public abstract class SimpleListAdapter<T> extends BaseAdapter{
      protected ArrayList<T> data;
  
      public SimpleListAdapter(){
          data = new ArrayList<T>(0);
      }
  
      public SimpleListAdapter(ArrayList<T> data){
          this.data = data;
          if(null == data){
              this.data = new ArrayList<>(0);
          }
      }
  
      public void setItems(ArrayList<T> items){
          this.data = items==null?new ArrayList<T>(0):items;
      }
  
      public void setItem(T item, int position){
          data.set(position, item);
      }
  
      public void clear(){
          data.clear();
      }
  
      public void addItem(T item){
          data.add(item);
      }
  
      public void addItem(T item, int position){
          data.add(position, item);
      }
  
      public void addItems(Collection<T> items){
          data.addAll(items);
      }
  
      public T removeItem(int position){
          return data.remove(position);
      }
  
      public boolean removeItem(T item){
          return data.remove(item);
      }
  
      @Override
      public int getCount() {
          return data.size();
      }
  
      @Override
      public T getItem(int position) {
          return data.get(position);
      }
  
      @Override
      public long getItemId(int position) {
          return position;
      }
  }
  
  ```

  

- 页面（Activity）

  ```java
  mData = new ArrayList<PostReportListBean.DataBean>();
  adapter = new PostReportsAdapter(this, mData);
  adapter.setPostReportListener(PostReportListActivity.this);
  listView.setAdapter(adapter);
  ```

  

### 问题

之前多人报告邮寄需要展示多个邮寄记录的同时，还要展示每个邮寄记录里面的体检人信息。可能大多数如果没做过类似的功能，第一眼就是使用ListView的item里面再嵌套一个ListView, 虽然这样可以完成，但是会导致设备过度绘制。因为当一个item加载的时候又去更新item里面的adapter,adapter又去更新自己的item，这样就会导致加载数据效率低下造成卡顿，并且使用不了复用机制。![](/Users/zy/Downloads/报告邮寄-前屏 (1).png)



### 解决思路

当我遇到这样的问题时候,其实我们可以把整个List,当做一个List处理。

![image-20201015201322918](_image/image-20201015201322918.png)

- **头部分为一个type**
- **内容分为一个type**
- 底部分为一个type

然后可以通过listview或者recyclerview的**getItemViewType**及getViewTypeCount方法来处理多个item类型



### 数据

```json
[
    {
        "code": "5F697799CAF3F4453477C8A8",
        "status": "待邮寄",
        "customerFullName": null,
        "reserveDate": null,
        "notifiedExpress": false,
        "expressCompanyCode": "5DAD04CEF51A9E0001A09D4F",
        "expressCompanyName": "顺丰速运(集团)有限公司",
        "customerPhone": null,
        "customerIdentityType": null,
        "customerIdentityNo": null,
        "examReportNo": null,
        "examReportNoTitle": "编码",
        "reportCount": 3,
        "packItems": [
            {
                "itemType": "体检报告",
                "merchantOrganCode": "YY18672310210338",
                "merchantOrganName": "见素杭州",
                "merchantOrganType": "医院",
                "ownerName": "Elena",
                "ownerSex": "女",
                "ownerMaritalStatus": "0",
                "ownerPhone": "17757171163",
                "ownerIdentityType": "身份证",
                "ownerIdentityNo": "360312197090901163",
                "ownerExamReportNo": null,
                "ownerExamDate": "2020-09-22",
                "itemCode": "ExamReportPost",
                "itemName": "报告邮寄",
                "itemSpec": "Elena 2020-09-22 体检",
                "itemCount": 1
            },
            {
                "itemType": "体检报告",
                "merchantOrganCode": "YY18672310210338",
                "merchantOrganName": "见素杭州",
                "merchantOrganType": "医院",
                "ownerName": "Elena",
                "ownerSex": "女",
                "ownerMaritalStatus": "0",
                "ownerPhone": "17757171163",
                "ownerIdentityType": "身份证",
                "ownerIdentityNo": "360312197090901163",
                "ownerExamReportNo": null,
                "ownerExamDate": "2020-09-22",
                "itemCode": "ExamReportPost",
                "itemName": "报告邮寄",
                "itemSpec": "Elena 2020-09-22 体检",
                "itemCount": 1
            }
        ]
    }
]
```

把一个申领记录数据拆分开来，拆成

- 头部数据
- 联系人数据（几个联系人拆成几个数据，使用时使用相应下标的联系人）
- 底部数据

处理数据的代码：

```java
 private List<ShopOrderListResponse.MsgBean> handleList(List<ShopOrderListResponse.MsgBean> list) {
        if (null == list || 0 == list.size())
            return list;
        List<ShopOrderListResponse.MsgBean> temp = new ArrayList<>();
        for (int i = 0; i < list.size(); i++) {//拆解一个订单
            bean = list.get(i);
            bean.setType(1);//给bean增加了一个type字段,所以这样可以用来set,为了在adapter里面去判断是什么类型
            temp.add(bean);//先增加一个头
            for (int j = 0; j < list.get(i).getOrderItem().size(); j++) {//拆解一个订单里面的具体每个订单
                bean = (ShopOrderListResponse.MsgBean) bean.clone();//这里用了深拷贝,是因为要对数据进行修改
                bean.setType(2);
                bean.setCustomerPosition(j);
                temp.add(bean);//增加一个内容
            }
            bean = (ShopOrderListResponse.MsgBean) bean.clone();
            bean.setType(3);
            temp.add(bean);//尾部添加一个
        }
        return temp;
    }
```

adapter代码：

```java
		@Override
    public int getItemViewType(int position) {
        return mData.get(position).getType();
    }
    @Override
    public int getViewTypeCount() {
        return 4;
    }

    @Override
    public int getCount() {
        return mData == null ? 0 : mData.size();
    }

    @Override
    public Object getItem(int position) {
        return mData.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(final int position, View convertView, ViewGroup parent) {
        YViewHolder holder = null;
        if (convertView == null) {
            holder = new YViewHolder();
            switch (getItemViewType(position)) {
                case 1: // head
                    convertView = View.inflate(mContext, R.layout.post_reports_item_head,null);
                    holder.tv_report_count = (TextView) convertView.findViewById(R.id.tv_report_count);
                    holder.tv_status  = (TextView) convertView.findViewById(R.id.tv_status);
                    break;
                case 2: // content
                    convertView = View.inflate(mContext, R.layout.post_reports_item_content,null);
                    holder.tv_reserve_date  = (TextView) convertView.findViewById(R.id.tv_reserve_date);
                    holder.tv_time_detail = (TextView) convertView.findViewById(R.id.tv_time_detail);
                    holder.tv_name  = (TextView) convertView.findViewById(R.id.tv_name);
                    holder.tv_phone  = (TextView) convertView.findViewById(R.id.tv_phone);
                    holder.tv_identify  = (TextView) convertView.findViewById(R.id.tv_identify);
                    holder.tv_report_code  = (TextView) convertView.findViewById(R.id.tv_report_code);
                    break;
                case 3: // footer
                    convertView = View.inflate(mContext, R.layout.post_reports_item_footer,null);
                    holder.bottom_ln = (LinearLayout) convertView.findViewById(R.id.bottom_ln);
                    holder.btn_post  = (Button) convertView.findViewById(R.id.btn_post);
                    break;
            }
            convertView.setTag(holder);
        }else {
            holder = (YViewHolder) convertView.getTag();
        }
        switch (getItemViewType(position)) {
            case 1: // head
                holder.tv_report_count.setText("体检报告("+ mData.get(position).getReportCount() + "份)");
                holder.tv_status.setText(mData.get(position).getStatus());
                String status = mData.get(position).getStatus();
                if ("待邮寄".equals(status)) {
                    holder.tv_status.setTextColor(Color.parseColor("#FF695C"));
                }else if ("已邮寄".equals(status)) {
                    holder.tv_status.setTextColor(Color.parseColor("#4983F5"));
                }else if ("已签收".equals(status)) {
                    holder.tv_status.setTextColor(Color.parseColor("#20BFB9"));
                }else if ("已取消".equals(status)) {
                    holder.tv_status.setTextColor(Color.parseColor("#B0B0B0"));
                }
                break;
            case 2: // 
                int tempP = mData.get(position).getCustomerPosition();
                holder.tv_name.setText(mData.get(position).getPackItems().get(tempP).getOwnerName());
                holder.tv_phone.setText(mData.get(position).getPackItems().get(tempP).getOwnerPhone());
                holder.tv_identify.setText(mData.get(position).getPackItems().get(tempP).getOwnerIdentityType() +  "：" + mData.get(position).getPackItems().get(tempP).getOwnerIdentityNo());
                holder.tv_reserve_date.setText("体检日期：" + mData.get(position).getPackItems().get(tempP).getOwnerExamDate());
                long time = getTimeDistance(stringToDate(mData.get(position).getPackItems().get(tempP).getOwnerExamDate()), new Date());
                if (time > 0) {
                    // 体检过去了多少天
                    String days = "体检距今" + time + "天";
                    holder.tv_time_detail.setText(days);
                } else {
                    holder.tv_time_detail.setText("");
                }
                if (mData.get(position).getExamReportNoTitle() == null) {
                    holder.tv_report_code.setVisibility(View.GONE);
                } else {
                    String code = mData.get(position).getPackItems().get(tempP).getOwnerExamReportNo() == null ? "" : mData.get(position).getPackItems().get(tempP).getOwnerExamReportNo();
                    holder.tv_report_code.setText(mData.get(position).getExamReportNoTitle() + "：" + code);
                }
                break;
            case 3: // head
                String status2 = mData.get(position).getStatus();
                if ("待邮寄".equals(status2)) {
                    holder.bottom_ln.setVisibility(View.VISIBLE);
                    holder.btn_post.setOnClickListener(new View.OnClickListener() {
                        @Override
                        public void onClick(View view) {
                            if (postReportListener!= null) {
                                postReportListener.postReportClick(position);
                            }
                        }
                    });
                }else {
                    holder.bottom_ln.setVisibility(View.GONE);
                }
                break;
        }
        return convertView;
    }

```

### 总结

我们可以用ListView的这种方式去实现复杂类型的列表。并且不卡，因为listView自带了复用机制，页面渲染一次之后，下次只是改变数据。所以经过这样我们就可以随意做复杂的列表而且不卡顿.

