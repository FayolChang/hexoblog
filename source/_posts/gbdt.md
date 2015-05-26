title: "gbdt"
date: 2015-05-25 21:41:11
tags: [machine learning, boosting]
---

# GBDT

关于机器学习，我目前看到的最好的中文书籍是《统计学习方法》，讲解的清晰易懂，而且有算法。看了之后都没有写东西的冲动了。

    关于 GBDT，在该书中是放在第八章提升方法提升树这一节。该章首先介绍了 adaboost，关键是每次调整样本的权重，预测对了权重就调低，错了就调高。如何实现这一点呢？用得是
$$
\alpha_m = \frac{1}{2}log \frac{1-e_m}{e_m}
$$
其中$e_m$是预测错误率。

然后介绍的是前向分步算法，是一个解决加法模型的算法。紧接着就证明了，如果损失函数取指数损失函数的话，那么能得出 adaboost，也就是说，adaboost 是前向分步算法的一个特列。

紧接着，在提升树算法中，如果采用平方误差损失的话，损失变成了

$$
L(y,f_{m-1}(x)+T(x;\Theta_m))=[y-f_{m-1}(x)-T(x;\Theta_m)]^2=[r-T(x;\Theta_m)]^2
$$

其中，r 是当前模型拟合的残差

所以，对于回归问题的提升树来说，只需拟合当前模型的残差即可。

我觉得这很有意思，以前是要修改样本的权重，到现在是要拟合残差，和样本权重好像没关系了

最后介绍的是梯度提升，说如果的是利用损失函数的负梯度在当前模型的值
$$
-\bigg[\frac{\partial L(y,\;f(x_{i}))}{\partial f(x_{i})}\bigg]_{f(x)=f_{m-1}(x)}
$$
来作为回归问题提升树算法中得残差近似值。

这里给出了 R 的[博客](http://xccds1977.blogspot.com/2015/05/boosting.html)

[代码](https://gist.githubusercontent.com/xccds/432b8752a2f9c14e3148/raw/gbm.R)

~~~R

    # Learn Gradient Boosting Model by coding
    
    # good slide
    # http://www.ccs.neu.edu/home/vip/teach/MLcourse/4_boosting/slides/gradient_boosting.pdf
    # http://cran.r-project.org/web/packages/gbm/gbm.pdf
    
    #1 Gradient Boosting for Regression
    
    # generate data
    generate_func = function(n=1000,size=0.5){
      # n: how many data points
      # size: hold 50% of all data as train 
      set.seed(1)
      x = seq(0,2*pi,length=n) # generate x
      noise = rnorm(n,0,0.3)
      y = sin(x) + noise # generate y
      select_index = sample(1:n,size=size*n,replace = F)
      # split data into train and test
      train_x = x[select_index]
      train_y = y[select_index]
      test_x = x[-select_index]
      test_y = y[-select_index]
      data = list('train_x'=train_x,
                 'train_y'=train_y,
                 'test_x'=test_x,
                 'test_y'=test_y)
      return(data)
    }
    
    data = generate_func()
    objects(data)
    
    # train boosting regression tree
    GBR = function(x,y,rate=1,iter=100){
      # iter is iterate number, higher is better, but be carefule overfit.
      # rate is learning speed, lower is better, but too slow will cause longer time. 
      library(rpart)
      max_depth=1 # tree stump
      mu = mean(y) # start with an initial model
      dy = y - mu  # residuals or error,  These are the parts that existing model cannot do well.
      learner = list() # define a learners holder 
      for (i in 1:iter) {
        # use a weak model to improve
        learner[[i]] = rpart(dy~x, # error is target variable
                             control=rpart.control(maxdepth=max_depth,cp=0)) # cp=0 means to growth a tree with any cost
        # modify residuals for next iter
        dy = dy - rate*predict(learner[[i]])
      }
      result = list('learner'=learner,
                    'rate'=rate,
                    'iter'=iter)
      return(result)
    }
    
    
    model = GBR(data$train_x,data$train_y,iter=1000)
    objects(model)
    
    # predict function
    predict_GBR = function(x,y,model){
      predict_y = list() # hold predict result
      mu = mean(y) # initial model
      n = length(y)
      iter = model$iter
      rate = model$rate
      predict_y[[1]] = rep(mu,n)
      learner = model$learner
      for (i in 2:iter) {
        # add pre-model prediction to predict y
        predict_y[[i]] = predict_y[[i-1]] + rate * predict(learner[[i-1]],newdata=data.frame(x))
      }
      # mean sqare error
      mse = sapply(predict_y,function(pre_y) round(mean((y-pre_y)^2),3))
      result = list('predict_y'=predict_y,
                    'mse'= mse)
      return(result)
    }
    
    # predict train data
    predict_train = predict_GBR(data$train_x,data$train_y,model=model)
    objects(predict_train)
    
    # more trees get less error
    plot(predict_train$mse,type='l')
    
    
    # plot the effect of boosing tree
    plotfunc = function(x,y,predict,num){
      library(ggplot2)
      pre = predict$predict_y[[num]] 
      plotdf = data.frame(x=x,y=y,pre = pre)
      mse = round(mean((y-pre)^2),3)
      p = ggplot(plotdf,aes(x,y)) +
        geom_point(alpha=0.5)
      p = p + geom_line(aes(x,pre)) + xlab(paste0('mse=',mse))
      plot(p)
    }
    
    # use mean of y to predict data
    plotfunc(data$train_x,data$train_y,predict_train,num=1)
    # add another tree model
    plotfunc(data$train_x,data$train_y,predict_train,num=2)
    # add more tree getter more result
    plotfunc(data$train_x,data$train_y,predict_train,num=10)
    plotfunc(data$train_x,data$train_y,predict_train,num=100)
    
    # predict test data
    predict_test = predict_GBR(data$test_x,data$test_y,model=model)
    plot(predict_test$mse,type='l')
    
    plotfunc(data$test_x,data$test_y,predict_test,10)
    plotfunc(data$test_x,data$test_y,predict_test,100)
    
    # compare different parametter
    # create 2 models with different rate
    model_1 = GBR(data$train_x,data$train_y,rate=1,iter=500)
    model_2 = GBR(data$train_x,data$train_y,rate=0.1,iter=500)
    
    # use train and test data, we have 4 results
    predict_train_1 = predict_GBR(data$train_x,data$train_y,model=model_1)
    predict_train_2 = predict_GBR(data$train_x,data$train_y,model=model_2)
    predict_test_1 = predict_GBR(data$test_x,data$test_y,model=model_1)
    predict_test_2 = predict_GBR(data$test_x,data$test_y,model=model_2)
    
    # take out mse of these 4 results
    train_error_1 = predict_train_1$mse
    train_error_2 = predict_train_2$mse
    test_error_1 = predict_test_1$mse
    test_error_2 = predict_test_2$mse
    iter = 1:model_1$iter
    
    # compare these mse
    plotdf = data.frame(iter,train_error_1,test_error_1,train_error_2,test_error_2)
    
    p = ggplot(plotdf)+
        geom_line(aes(x=iter,y=train_error_1),color='blue')+
        geom_line(aes(x=iter,y=test_error_1),color='red') +
      geom_line(aes(x=iter,y=train_error_2),linetype=2,color='blue')+
      geom_line(aes(x=iter,y=test_error_2),linetype=2,color='red')
    print(p)
    
    # test error is better model performance.
    # less rate is better but need more iter
    which.min(train_error_1)
    which.min(train_error_2)
    which.min(test_error_1)
    which.min(test_error_2)
    
    # 2. Gradient Boosting for Classification
    
    # read data
    data = subset(iris,Species!='virginica',select = c(1,2,5))
    data$y = ifelse(data$Species == 'setosa',1,-1)
    data$Species = NULL
    names(data)[1:2] = c('x1','x2')
    head(data)
    p = ggplot(data,aes(x1,x2,color=factor(y)))+
        geom_point()
    print(p)
    
    # train boosting tree for classification
    GBC = function(data,rate=0.1,iter=100){
      library(rpart)
      max_depth=1
      learner = list()
      mu = mean(data$y==1)
      # start with an initial model
      # mu=p(y=1) -> f=w*x = log(mu/(1-mu)) 
      f = log(mu/(1-mu)) 
      data$dy = data$y/(1+exp(data$y*f)) # dy is negtive gradient of log loss funtion
      for (i in 1:iter) {
        # use a weak model to improve
        learner[[i]] = rpart(dy~x1+x2,data=data,
                             control=rpart.control(maxdepth=max_depth,cp=0))
        # improve model 
        f = f + rate *predict(learner[[i]])
        # modify dy
        data$dy = data$y/(1+exp(data$y*f))
      }
      result = list('learner'=learner,
                    'rate'=rate,
                    'iter'=iter)
      return(result)
    }
    
    model = GBC(data,rate=0.1,iter=1000)
    objects(model)
    
    # predict function
    predict_GBC = function(data,model){
      predict_y = list()
      mu = mean(data$y==1)
      f = log(mu/(1-mu)) 
      n = nrow(data)
      iter = model$iter
      rate = model$rate
      predict_y[[1]] = rep(f,n)
      learner = model$learner
      for (i in 2:iter) {
        predict_y[[i]] = predict_y[[i-1]] + rate *predict(learner[[i-1]],newdata=data)
      }
      mse = sapply(predict_y,function(pre_y) sum(log(1+exp(-data$y*pre_y)))) # logistic loss function
      result = list('predict_y'=predict_y,
                    'mse'= mse)
      return(result)
    }
    
    # predict data
    predict_train = predict_GBC(data,model=model)
    objects(predict_train)
    plot(predict_train$mse,type='l')
    
    final = predict_train$predict_y[[1000]]
    y_p = 1/(1+exp(-final))
    y_pred = ifelse(y_p>0.5,1,-1)
    table(data$y, y_pred)
    
    # # plot
    
    plotfunc2 = function(data,num,rate=0.1){
      library(ggplot2)
      model = GBC(data,rate=rate,iter=num)
      predict_train = predict_GBC(data,model=model)
      final = predict_train$predict_y[[num]]
      y_p = 1/(1+exp(-final))
      data$y_pre = ifelse(y_p>0.5,1,-1)
      tree_f = sapply(model$learner,function(x){
        temp = x$splits
        return(row.names(temp)[1])
      })
      tree_v = sapply(model$learner,function(x){
        temp = x$splits
        return(temp[1,'index'])
      })
      x1value = tree_v[tree_f=='x1']
      x2value = tree_v[tree_f=='x2']
      p = ggplot(data,aes(x=x1,y=x2))
      p = p + geom_point(aes(color=factor(y_pre)),size=5) +
        geom_point(aes(color=factor(y)),size=3)+
        geom_vline(xintercept = x1value,alpha=0.4) +
        geom_hline(yintercept = x2value,alpha=0.4) +  
        scale_colour_brewer(type="seq", palette='Set1') +
        theme_bw()
      print(p)
    }
    
    plotfunc2(data,num=10)
    plotfunc2(data,num=20)
    plotfunc2(data,num=60)
    plotfunc2(data,num=100)
    plotfunc2(data,num=1000)
~~~