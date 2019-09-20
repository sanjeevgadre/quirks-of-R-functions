Linear Regression using Small Subsets
=====================================

The most convenient way to build a linear regression model is to use the
`lm()` function that is part of the base `stats` library. While
convenient, `lm` accepts parameters only in a single format:

`fit = lm(response ~ terms, data = dat, ....)`;

This turns out to be restrictive when working with a small subset of a
dataset that has a `factor` variable with a skewed distribution. This
happens to be the case specifically when one is constructing a *learning
curve*.

In a small subsetm, it is likely that not all the classes of a `factor`
variable find their way into the selection. If we fit a linear
regression to the subset, `lm()`, on finding that not all the classes of
the `factor` variable are present in the data, returns an error.

    Error in `contrasts<-`(`*tmp*`, value = contr.funs[1 + isOF[nn]]) : 
      contrasts can be applied only to factors with 2 or more levels

In a situation like this one is forced to use the lower level `lm.fit()`
function. The *quirks* to manage:

1.  `lm.fit()` requires the use of a model matrix
2.  The `lm.fit` returns a `list` variable and its elements need to be
    parsed carefully when using the fitted model to make predictions.
3.  Specifically, it is to be noted that since not all levels of the
    `factor` variable are present in the training data, the model might
    return `NA` as coefficients for some of these levels. This needs to
    be handled carefully.
4.  Moreover, the test dataset for which the predictions are made need
    to be in a model matrix format with the same structure as the
    training dataset. It is recommended that the training and the test
    datasets be combined and then a model matrix be formed

*SAMPLE CODE:*

    test$response = << some reasonable dummy value >>
    indx = 1:nrow(train)
    dat = rbind(train, test)

    y = dat$response
    x = model.matrix(response ~ terms, data = dat)

    fit = lm.fit(x[indx, ], y[indx])

    fit.coeff = fit$coefficients
    fit.coeff = ifelse(is.na(fit.coeff), 0, fit.coeff)

    pred = x[-train, ]%*%fit.coeff
    pred = as.numeric(pred)
