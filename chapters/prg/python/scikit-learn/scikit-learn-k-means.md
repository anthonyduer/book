# Scikit-learn :o: :hand: sp19-516-122 {#s-sikitlearn}

Scikit-learn is a popular data analysis library that supports
the complete data lifecycle from preprocessing, analyzing, modeling,
and visualizing. It contains many algorithms that allow data scientists
to create robust data pipelines that makes data anlysis much more
efficient. Example of features that are included in scikit-learn are linear 
regression models, min-max preprocessors, and cross validators.

Standardization is an important feature of scikit-learn. For example,
each model uses a fit(x, y) or fit(x) method to fit a model to data.
This allows for easy model exploration as models can be collected into
a list and then iterated over to examine how each performs against the
data without fine tuning. Since scikit-learn sits atop the NumPy library,
it also benefits from sections of code written in cython, making it
very quick.

In this section, we provide a cursory overview of each of the data
lifecycle steps using toy data.

## Getting Started

If you already have a working installation of numpy and scipy, the
easiest way to install scikit-learn is using pip

    $ pip install numpy
    $ pip install scipy -U
    $ pip install -U scikit-learn
    
## Data Preprocessing

## Data Analysis

## Data Modeling

## Data Visualization


Import
------

    from time import time
    import numpy as np
    import matplotlib.pyplot as plt

    from sklearn import metrics
    from sklearn.cluster import KMeans
    from sklearn.datasets import load_digits
    from sklearn.decomposition import PCA
    from sklearn.preprocessing import scale

Create samples
--------------

    np.random.seed(42)

    digits = load_digits()
    data = scale(digits.data)

    n_samples, n_features = data.shape
    n_digits = len(np.unique(digits.target))
    labels = digits.target

    sample_size = 300

    print("n_digits: %d, \t n_samples %d, \t n_features %d"
          % (n_digits, n_samples, n_features))


    print(79 * '_')
    print('% 9s' % 'init'
          '    time  inertia    homo   compl  v-meas     ARI AMI  silhouette')


    def bench_k_means(estimator, name, data):
        t0 = time()
        estimator.fit(data)
        print('% 9s   %.2fs    %i   %.3f   %.3f   %.3f   %.3f   %.3f    %.3f'
              % (name, (time() - t0), estimator.inertia_,
                 metrics.homogeneity_score(labels, estimator.labels_),
                 metrics.completeness_score(labels, estimator.labels_),
                 metrics.v_measure_score(labels, estimator.labels_),
                 metrics.adjusted_rand_score(labels, estimator.labels_),
                 metrics.adjusted_mutual_info_score(labels,  estimator.labels_),
                 metrics.silhouette_score(data, estimator.labels_,
                                          metric='euclidean',
                                          sample_size=sample_size)))

    bench_k_means(KMeans(init='k-means++', n_clusters=n_digits, n_init=10),
                  name="k-means++", data=data)

    bench_k_means(KMeans(init='random', n_clusters=n_digits, n_init=10),
                  name="random", data=data)

    # in this case the seeding of the centers is deterministic, hence we run the
    # kmeans algorithm only once with n_init=1
    pca = PCA(n_components=n_digits).fit(data)
    bench_k_means(KMeans(init=pca.components_, 
                         n_clusters=n_digits, n_init=1),
                  name="PCA-based",
                  data=data)
    print(79 * '_')

Visualize
=========

    reduced_data = PCA(n_components=2).fit_transform(data)
    kmeans = KMeans(init='k-means++', n_clusters=n_digits, n_init=10)
    kmeans.fit(reduced_data)

    # Step size of the mesh. Decrease to increase the quality of the VQ.
    h = .02     # point in the mesh [x_min, x_max]x[y_min, y_max].

    # Plot the decision boundary. For that, we will assign a color to each
    x_min, x_max = reduced_data[:, 0].min() - 1, reduced_data[:, 0].max() + 1
    y_min, y_max = reduced_data[:, 1].min() - 1, reduced_data[:, 1].max() + 1
    xx, yy = np.meshgrid(np.arange(x_min, x_max, h), np.arange(y_min, y_max, h))

    # Obtain labels for each point in mesh. Use last trained model.
    Z = kmeans.predict(np.c_[xx.ravel(), yy.ravel()])

    # Put the result into a color plot
    Z = Z.reshape(xx.shape)
    plt.figure(1)
    plt.clf()
    plt.imshow(Z, interpolation='nearest',
               extent=(xx.min(), xx.max(), yy.min(), yy.max()),
               cmap=plt.cm.Paired,
               aspect='auto', origin='lower')

    plt.plot(reduced_data[:, 0], reduced_data[:, 1], 'k.', markersize=2)
    # Plot the centroids as a white X
    centroids = kmeans.cluster_centers_
    plt.scatter(centroids[:, 0], centroids[:, 1],
                marker='x', s=169, linewidths=3,
                color='w', zorder=10)
    plt.title('K-means clustering on the digits dataset (PCA-reduced data)\n'
              'Centroids are marked with white cross')
    plt.xlim(x_min, x_max)
    plt.ylim(y_min, y_max)
    plt.xticks(())
    plt.yticks(())
    plt.show()

![image](images/scikit-learn-k-means_10_0.png)
