The library `gbm` offers the `gbm()` function to build models using the
**Boosted Trees Ensemble** method. `gbm()` uses the familiar construct:

`fit = func_name(dep_var ~ pred_vars, data = dat, ....)`;

which in this case is:

`fit = gbm(dep_var ~ pred_vars, data = dat, ....)`

1.  Unlike other similar functions, `gbm()` explicitly expects `dep_var`
    be `numerical`. This quirk adds an extra step when using `gbm()` to
    fit *classification tree ensemble* model. Consequently, `dep_var`,
    which usually is a `factor` variable when fitting a classification
    tree ensemble, needs to be converted to a `numerical` variable
    before fitting the model.

*SAMPLE CODE:*

    library(gbm)

    train$dep_var = as.numeric(levels(train$dep_var))[train$dep_var]
    fit = gbm(dep_var ~ pred_vars, data = train, ....)

1.  The `predict.gbm()` function when used with a classification tree
    ensemble fit, does not return either the predicted class or the
    probability **P(y = 1|x)**. Instead, the function returns the
    **log-odds (logit)** values and these need to be converted, to
    derive P(y = 1|x), via an appropriate transformation.

*SAMPLE CODE:*

    pred_logit = predict.gbm(fit, newdata = test, ....)
    prob = exp(pred_logit)/(1 + exp(pred_logit))
