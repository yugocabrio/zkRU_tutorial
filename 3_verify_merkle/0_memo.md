### sample_get_merkle_root

                           root
                             |
                       [hash12]
                       /      \
                  [hash01]  [hash23]
                  /      \   /     \
                leaf0 leaf1 leaf2 leaf3


上記の図では、Merkle Treeは2段階(k=2)で構成されており、それぞれの段階で2つのハッシュが結合されて、より上位のハッシュが生成されます。1段階目では、2つの葉(leaf0とleaf1)のハッシュが結合されて、[hash01]というハッシュが生成されます。同様に、2段階目では、[hash01]と[leaf2]のハッシュが結合されて、最終的にルートとして[Root]というハッシュが生成されます。

このプログラムは、k段階のMerkle Treeのルートを計算するためのCircomテンプレートです。テンプレートは、leaf(葉)、paths2_root(2番目のパス)、およびpaths2_root_pos(2番目のパスの位置)を入力として受け取り、ルートハッシュを出力します。ルートは、k-1段階目のmerkle_root[k-1].outであり、これはすべての入力要素のハッシュを含むMerkle Treeのルートハッシュです。

このテンプレートは、MultiMiMC7という関数を使用して、各段階のMerkle Treeのノードをハッシュします。各段階のmerkle_root[v]には、v段階目のMerkle Treeのハッシュを計算するための2つの入力(in[0]とin[1])があります。この2つの入力は、paths2_rootとpaths2_root_posを使用して計算され、それらを使用して前段階のmerkle_root[v-1]の出力も取得します。

### sample_LeafExistence
leaf_existence.circom は、予想されるMerkle Rootと計算されたMerkle Rootを比較する回路です。

このコードは、Merkle Treeにおいて、あるリーフがTreeに存在するかどうかを検証するための回路を定義しています。

include "./sample_get_merkle_root.circom";とinclude "../circomlib/circuits/mimc.circom";は、他のcircomファイルから必要な回路をインポートするための命令です。ここではMerkle Treeのルートを取得するための回路GetMerkleRootと、MiMCハッシュ関数の回路が必要なため、それらをインポートしています。

LeafExistence(k)は、テンプレート関数で、kはMerkle Treeの深さを指定するためのパラメータです。テンプレート関数は、特定の型の入力に対して、同じ手順を複数回実行する必要がある場合に役立ちます。

この関数は、リーフとルートを入力とし、Merkle Treeの深さkに対するパスを指定して、指定されたリーフがMerkle Treeに存在するかどうかを検証するための回路を定義しています。

component computed_root = GetMerkleRoot(k);は、GetMerkleRoot関数を用いて、Merkle Treeのルートを計算するための回路を定義しています。computed_rootは、ルートを出力するコンポーネントです。

computed_root.leaf <== leaf;は、入力リーフleafをcomputed_rootコンポーネントの入力に接続しています。

for (var w = 0; w < k; w++)は、kに等しい回数繰り返しを行うためのループです。computed_root.paths2_root[w] <== paths2_root[w];とcomputed_root.paths2_root_pos[w] <== paths2_root_pos[w];は、各深さにおけるパスを計算するために必要な情報を、computed_rootコンポーネントの入力に接続しています。

最後に、root === computed_root.out;は、計算されたルートと入力ルートが等しいかどうかを比較して、リーフがMerkle Treeに存在するかどうかを検証するためのコードです。

component main = LeafExistence(2);は、LeafExistence関数に深さ2を与えて、検証回路を定義することを意味しています。この検証回路は、mainコンポーネントに割り当てられ、これが実際に実行される回路です。