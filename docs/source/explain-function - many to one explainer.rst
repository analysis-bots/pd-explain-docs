.. _explain-function - Many to One Explainer:

Many to One Explainer
=====================
The **Many to One Explainer** creates rule based explanations for many to one relationships.
It provides insights into how the input features define groups of output features.

Method Signature
-----------------------------------
.. code-block:: python

   ExpDataFrame.explain(
    explainer: Literal['fedex', 'outlier', 'many_to_one', 'shapley', 'metainsight']='fedex',
    attributes: List = None,
    use_sampling: bool | None = None,
    sample_size: int | float = 5000
    labels=None, coverage_threshold: float = 0.7,
    max_explanation_length: int = 3,
    separation_threshold: float = 0.3,
    p_value: int = 1,
    explanation_form: Literal['conj', 'disj', 'conjunction', 'disjunction'] = 'conj',
    prune_if_too_many_labels: bool = True,
    max_labels: int = 10,
    pruning_method='largest',
    bin_numeric: bool = False,
    num_bins: int = 10,
    binning_method: str = 'quantile',
    label_name: str = 'label',
    explain_errors=True,
    error_explanation_threshold: float = 0.05,
   )

Many to One Explainer Usage Example
-----------------------------------
.. code-block:: python

    # Import the necessary libraries
    import pandas as pd
    import pd_explain

    # Load the "adult" dataset
    adult = pd.read_csv(r'C:\adult.csv')

    # Call the many to one explainer
    adult.explain(explainer='many_to_one', labels='class')

**Output**:
.. table::

    +-----------------+----------------------------------------------------------+----------+------------------+--------------------------+
    | Group / Cluster | Explanation                                              | Coverage | Separation Error | Separation Error Origins |
    +=================+==========================================================+==========+==================+==========================+
    | <=50K           | 1 <= education-num <= 10                                 | 0.75     | 0.15             | 100.00% from group >50K  |
    | <=50k           | 0 <= capital-gain <= 5095.5                              | 1.0      | 0.21             | 100.00% from group >50K  |
    | <=50k           | 0 <= capital-gain <= 5095.5 AND 1 <= education-num <= 10 | 0.75     | 0.13             | 100.00% from group >50K  |
    | <=50k           | 0 <= capital-gain <= 4243.5                              | 0.99     | 0.2              | 100.00% from group >50K  |
    | >50K            | No explanation found                                     | NaN      | NaN              | NaN                      |
    +-----------------+----------------------------------------------------------+----------+------------------+--------------------------+

Coverage is the % of the group that is covered by the explanation.
Separation Error is the % of data outside the group that is covered by the explanation.

Parameters
-----------------------------------
- ``explainer`` (str): The explainer to use. This is shared with other explainers, but for the many to one explainer, it must be set to ``many_to_one``.
- ``attributes`` (list, optional): The attributes to consider when generating explanations. Default is ``None``.
- ``use_sampling`` (bool | None, optional): Whether to use sampling to speed up the computation. Default is to use the global setting.
- ``sample_size`` (int | float, optional): The number of samples to use. Default is ``5000``. Using a float between ``0`` and ``1`` will use that fraction of the data.
- ``labels`` (str | list | Series | DataFrame | ndarray | None): The labels defining the many to one relationship. Can be a name (or list of names) of a column in the DataFrame, a Series, a DataFrame, a numpy array, or None. None is only applicable for when the explainer is called on the result of a GroupBy operation, in which case the GroupBy groups will be inferred automatically. Otherwise, the labels must be provided. Defaults to `None`.
- ``coverage_threshold`` (float, optional): The minimum coverage required for an explanation to be considered. Default is ``0.7``.
- ``separation_threshold`` (float, optional): The minimum separation error required for an explanation to be considered. Default is ``0.3``.
- ``max_explanation_length`` (int, optional): The maximum number of conditions in an explanation. Default is ``3``.
- ``p_value`` (float, optional): A scaling parameter for the number of top attributes to consider when generating explanations. Number of attributes to consider = ``p_value`` * ``max_explanation_length``. Default is ``1``.
- ``explanation_form`` (str, optional): The form of the explanation. Default is ``conjunction``. Other options are ``disjunction``, or short forms ``conj`` and ``disj``.
- ``prune_if_too_many_labels`` (bool, optional): Whether to prune the labels to a smaller subset if there are too many. Default is ``True``.
- ``max_labels`` (int, optional): The number of labels to keep if ``prune_if_too_many_labels`` is ``True``. If there are less labels, no pruning will be performed. Default is ``10``.
- ``pruning_method`` (str, optional): The method to use for pruning labels. The options are:

    - ``largest``: Keeps the k most frequent labels.
    - ``smallest``: Keeps the k least frequent labels.
    - ``random``: Keeps k random labels.
    - ``max_dist``: Keeps the k labels with the largest mean distance between their centroids and the centroids of other labels.
    - ``min_dist``: Keeps the k labels with the smallest mean distance between their centroids and the centroids of other labels.
    - ``max_silhouette``: Keeps k labels with the largest silhouette score.
    - ``min_silhouette``: Keeps k labels with the smallest silhouette score.
    Default is ``largest``.

- ``bin_numeric`` (bool, optional): If the labels are numeric, whether to bin them into categories. Default is ``False``.
- ``num_bins`` (int, optional): The number of bins to use if ``bin_numeric`` is ``True``. If there are less unique values than ``num_bins``, no binning will be performed. Default is ``10``.
- ``bin_method`` (str, optional): The method to use for binning. The options are:

    - ``uniform``: Bins are of equal width.
    - ``quantile``: Bins are of equal frequency.
    Default is ``quantile``.

- ``label_name`` (str, optional): The name to give the labels if they are binned. Default is ``Label``. Only needed if the labels do not come from a Series / DataFrame with a name, and will only affect its display in the explanation. For example, you may see ``x <= label <= y`` as a group name.
- ``explain_errors`` (bool, optional): Whether to provide explanations for the origins of the separation error. Default is ``True``.
- ``error_explanation_threshold`` (float, optional): The threshold for much a group must individually contribute to the separation error to appear in the explanation. Groups that contribute less than this will be grouped together. Default is ``0.05``.

Other Usage Examples
--------------------
We will now show other examples of how to use the **many to one explainer** with different parameters.

Example 1: Explaining Clustering Results
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The **many to one explainer** works on any many-to-one relationship, including clustering results.

.. code-block:: python

    # Import the necessary libraries
    import pandas as pd
    import pd_explain
    from sklearn.cluster import KMeans

    # Load the adult dataset
    adult = pd.read_csv(r'C:\adult.csv')

    # Perform a clustering operation
    clusters = KMeans(n_clusters=3).fit_predict(adult)

    # Call the many to one explainer
    adult.explain(explainer='many_to_one', labels=clusters)

**Output**:
.. table::
    +-----------------+----------------------------------------------------------------+----------+------------------+-------------------------------+
    | Group / Cluster | Explanation                                                    | Coverage | Separation Error | Separation Error Origins      |
    +=================+================================================================+==========+==================+===============================+
    | 0               | 149278.5 <= fnlwgt <= 1490400                                  | 1.0      | 0.22             | 100.00% from group 1          |
    | 0               | 149278.5 <= fnlwgt <= 1490400 AND 8.5 <= education-num <= 16.0 | 0.87     | 0.21             | 100.00% from group 1          |
    | 1               | 291277.5 <= fnlwgt <= 1490400                                  | 1.0      | 0.0              | Rule has no separation error. |
    | 2               | 13769 <= fnlwgt <= 149278.5                                    | 1.0      | 0.0              | Rule has no separation error. |
    +-----------------+----------------------------------------------------------------+----------+------------------+-------------------------------+


Example 2: Explaining GroupBy Groups
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If you perform a group-by operation, you can then call the many to one explainer on the result to get insights into the groups.
Simply leave the ``labels`` parameter as ``None`` to infer the groups from the DataFrame.
Note that it is only with group-by operations that you can leave the ``labels`` parameter as ``None``, any other case requires you to provide the labels.

.. code-block:: python

    # Import the necessary libraries
    import pandas as pd
    import pd_explain

    # Load the adult dataset
    adult = pd.read_csv(r'C:\adult.csv')

    # Perform a group by operation
    gb_res = adult.groupby(['workclass', 'marital-status']).mean()

    # Call the many to one explainer, with some additional optional parameters to customize the output
    gb_res.explain(explainer='many_to_one', pruning_method='random', max_labels=3)

**Output**:
.. table::
    +---------------------------------------------+-----------------------------------------+----------+------------------+-----------------------------------------------------------------------------------------------------------------------------+
    | Group / Cluster                             | Explanation                             | Coverage | Separation Error | Separation Error Origins                                                                                                    |
    +=============================================+=========================================+==========+==================+=============================================================================================================================+
    | (' Self-emp-inc', ' Separated')             | 26 <= age <= 69                         | 1.0      | 0.23             | 83.33% from group (' Self-emp-inc', ' Married-spouse-absent'), 16.67% from group (' Without-pay', ' Married-spouse-absent') |
    | (' Self-emp-inc', ' Separated')             | occupation !=  Farming-fishing          | 0.95     | 0.17             | 100.00% from group (' Self-emp-inc', ' Married-spouse-absent')                                                              |
    | (' Self-emp-inc', ' Married-spouse-absent') | sex !=  Female AND occupation ==  Sales | 0.8      | 0.0              | Rule has no separation error.                                                                                               |
    | (' Self-emp-inc', ' Married-spouse-absent') | sex ==  Male AND occupation ==  Sales   | 0.8      | 0.0              | Rule has no separation error.                                                                                               |
    | (' Without-pay', ' Married-spouse-absent')  | age == 68                               | 1.0      | 0.0              | Rule has no separation error.                                                                                               |
    +---------------------------------------------+-----------------------------------------+----------+------------------+-----------------------------------------------------------------------------------------------------------------------------+

Example 3: Disjunctive Explanations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The many to one explainer can provide explanations based on either conjunctive or disjunctive rules.
To get disjunctive explanations, set the ``explanation_form`` parameter to ``disj`` or ``disjunctive``.

.. code-block:: python

    # Import the necessary libraries
    import pandas as pd
    import pd_explain

    # Load the adult dataset
    adult = pd.read_csv(r'C:\adult.csv')

    # Call the many to one explainer with disjunctive explanations,
    # as well as select only the categorical attributes to consider, and disable sampling for more accurate (but slower) results.
    adult.explain(explainer='many_to_one', explanation_form='disj', labels='label',
                    attributes=['workclass', 'education', 'marital-status', 'occupation', 'relationship'], use_sampling=False)

**Output**:
.. table::
    +-----------------+--------------------------------------------------------+----------+------------------+--------------------------+
    | Group / Cluster | Explanation                                            | Coverage | Separation Error | Separation Error Origins |
    +=================+========================================================+==========+==================+==========================+
    | <=50K           | occupation != Prof-specialty OR education != Bachelors | 0.96     | 0.23             | 100.00% from group >50K  |
    | <=50K           | occupation != Prof-specialty                           | 0.91     | 0.21             | 100.00% from group >50K  |
    | >50K            | No explanation found                                   | NaN      | NaN              | NaN                      |
    +-----------------+--------------------------------------------------------+----------+------------------+--------------------------+

Example 4: Passing a DataFrame as Labels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You can pass a DataFrame with more than one column as the labels, and not just a single column.
Doing so each unique combination of the columns will be considered as a separate label, much like in the case of a group-by operation.

.. code-block:: python

    # Import the necessary libraries
    import pandas as pd
    import pd_explain

    # Load the "adult" dataset
    adult = pd.read_csv(r'C:\adult.csv')

    # Select the labels
    labels = adult[['workclass', 'marital-status']]

    adult.drop(columns=['workclass', 'marital-status']).explain(explainer='many_to_one', labels=labels, pruning_method='min_dist', max_labels=3)

**Output**:
.. table::

    +---------------------------------------+--------------------------------------------------+----------+------------------+--------------------------------------------------------------------------------------------------------+
    | Group / Cluster                       | Explanation                                      | Coverage | Separation Error | Separation Error Origins                                                                               |
    +=======================================+==================================================+==========+==================+========================================================================================================+
    | ('State-gov', 'Never-married')        | relationship != Husband AND relationship != Wife | 1.0      | 0.05             | 85.71% from group ('?', 'Married-civ-spouse'), 14.29% from group ('Federal-gov', 'Married-civ-spouse') |
    | ('Federal-gov', 'Married-civ-spouse') | occupation != ? AND relationship == Husband      | 0.91     | 0.0              | Rule has no separation error.                                                                          |
    | ('?', 'Married-civ-spouse')           | occupation == ?                                  | 1.0      | 0.0              | Rule has no separation error.                                                                          |
    +---------------------------------------+--------------------------------------------------+----------+------------------+--------------------------------------------------------------------------------------------------------+


Example 5: Binning Numeric Labels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If your labels are numeric, you can bin them into categories to get more meaningful explanations.
To do this, set the ``bin_numeric`` parameter to ``True``, and optionally set the ``num_bins`` parameter to control the number of bins.

.. code-block:: python

    # Import the necessary libraries
    import pandas as pd
    import pd_explain

    # Load the "adult" dataset
    adult = pd.read_csv(r'C:\adult.csv')

    # Call the many to one explainer, setting the bin_numeric parameter to True, and using a custom number of bins
    adult.explain(explainer='many_to_one', labels='education-num', bin_numeric=True, num_bins=4)

**Output**:
.. table::

    +----------------------+------------------------------------------------------+----------+------------------+--------------------------------------------------------------------------------+
    | Group / Cluster      | Explanation                                          | Coverage | Separation Error | Separation Error Origins                                                       |
    +======================+======================================================+==========+==================+================================================================================+
    | 0.999 < education-num <= 9.0 | education != Some-college AND education != Bachelors | 1.0      | 0.27             | 52.16% from group 13.0 < label <= 16.0, 47.84% from group 10.0 < label <= 13.0 |
    | 9.0 < education-num <= 10.0  | education == Some-college                            | 1.0      | 0.0              | Rule has no separation error.                                                  |
    | 10.0 < education-num <= 13.0 | No explanation found                                 | NaN      | NaN              | NaN                                                                            |
    | 13.0 < education-num <= 16.0 | No explanation found                                 | NaN      | NaN              | NaN                                                                            |
    +----------------------+------------------------------------------------------+----------+------------------+--------------------------------------------------------------------------------+


In this example, since the `education-num` column came from our dataframe, it had a name to display.
Let's instead provide it as a numpy array, and see how the output changes.

.. code-block:: python

    # Import the necessary libraries
    import pandas as pd
    import pd_explain

    # Load the "adult" dataset
    adult = pd.read_csv(r'C:\adult.csv')

    # Call the many to one explainer, setting the bin_numeric parameter to True, and using a custom number of bins
    adult.drop(columns='education-num').explain(explainer='many_to_one', labels=adult['education-num'].values, bin_numeric=True, num_bins=4)

**Output**:
.. table::

    +----------------------+------------------------------------------------------+----------+------------------+--------------------------------------------------------------------------------+
    | Group / Cluster      | Explanation                                          | Coverage | Separation Error | Separation Error Origins                                                       |
    +======================+======================================================+==========+==================+================================================================================+
    | 0.999 < label <= 9.0 | education != Some-college AND education != Bachelors | 1.0      | 0.27             | 52.16% from group 12.0 < label <= 16.0, 47.84% from group 10.0 < label <= 12.0 |
    | 9.0 < label <= 10.0  | education == Some-college                            | 1.0      | 0.0              | Rule has no separation error.                                                  |
    | 10.0 < label <= 12.0 | No explanation found                                 | NaN      | NaN              | NaN                                                                            |
    | 12.0 < label <= 16.0 | No explanation found                                 | NaN      | NaN              | NaN                                                                            |
    +----------------------+------------------------------------------------------+----------+------------------+--------------------------------------------------------------------------------+

As you can see, the output now displays the label as `label` instead of `education-num`.
If we want to change this, we can use the ``label_name`` parameter.

.. code-block:: python

    # Import the necessary libraries
    import pandas as pd
    import pd_explain

    # Load the "adult" dataset
    adult = pd.read_csv(r'C:\adult.csv')

    # Call the many to one explainer, setting the bin_numeric parameter to True, and using a custom number of bins
    adult.drop(columns='education-num').explain(explainer='many_to_one', labels=adult['education-num'].values, bin_numeric=True, num_bins=4, label_name='Education number')

**Output**:
.. table::

    +---------------------------------+------------------------------------------------------+----------+------------------+------------------------------------------------------------------------------------------------------+
    | Group / Cluster                 | Explanation                                          | Coverage | Separation Error | Separation Error Origins                                                                             |
    +=================================+======================================================+==========+==================+======================================================================================================+
    | 0.999 < Education number <= 9.0 | education != Some-college AND education != Bachelors | 1.0      | 0.27             | 52.16% from group 12.0 < Education number <= 16.0, 47.84% from group 10.0 < Education number <= 12.0 |
    | 9.0 < Education number <= 10.0  | education == Some-college                            | 1.0      | 0.0              | Rule has no separation error.                                                                        |
    | 10.0 < Education number <= 12.0 | No explanation found                                 | NaN      | NaN              | NaN                                                                                                  |
    | 12.0 < Education number <= 16.0 | No explanation found                                 | NaN      | NaN              | NaN                                                                                                  |
    +---------------------------------+------------------------------------------------------+----------+------------------+------------------------------------------------------------------------------------------------------+
